# CSC2106 IoT Study Notes

## How To Read These Notes (What / Does / Why)

For every concept, revise using this 3-step frame:
1. What is this: define the component or protocol element.
2. What does it do: describe runtime behavior or data flow role.
3. Why is it done: explain engineering reason or tradeoff.

Quick examples:
- REST `POST /led`
  - What: endpoint for LED state update.
  - Does: parses request body and returns command for main loop actuation.
  - Why: separates remote control intent from direct GPIO code.
- MQTT retained status topic
  - What: broker-stored latest status message.
  - Does: immediately delivers current state to late subscribers.
  - Why: monitoring clients should not wait for next periodic publish.
- BLE CCCD
  - What: notification subscription descriptor.
  - Does: enables notify when client writes `0x0100`.
  - Why: push traffic should be explicit and per-connection.
- LoRa ACK + retry
  - What: reliability mechanism in application protocol.
  - Does: retransmits when ACK not received before timeout.
  - Why: radio loss/interference is normal; retry improves delivery probability.

## Beginner First 30 Minutes (No Prior Background)

If you are totally new, use this order:
1. Understand communication model per lab:
   - REST: request-response (client asks, server replies).
   - MQTT: publish-subscribe via broker.
   - BLE GATT: central-peripheral connection with characteristics.
   - BLE flooding mesh demo: advertise/scan rebroadcast with TTL + dedupe.
   - LoRa P2P: packet radio with ACK/NACK retries.
2. Learn one full packet/request journey in each lab.
3. Learn failure handling paths (timeouts, invalid payload, reconnect, dedupe).
4. Learn only then the implementation details in code.

Use this quick self-check for each concept:
- Can I define it in one sentence?
- Can I explain where it appears in runtime flow?
- Can I explain what breaks if it is removed?

---

## Lab 1 — RESTful API on Pico W

### Core Concepts
- **REST (Representational State Transfer)**: architectural style for web APIs using HTTP methods.
- **HTTP Methods used:**
  - `GET` — retrieve data (read-only, no body change)
  - `POST` — send data to modify state (e.g. set LED)
- **HTTP Status Codes:**
  - `200 OK` — success
  - `400 Bad Request` — malformed body/missing fields
  - `404 Not Found` — unknown route
  - `405 Method Not Allowed` — wrong HTTP method for that route

### Pico W REST Endpoints

| Method | Route     | Description                        |
|--------|-----------|------------------------------------|
| GET    | `/`       | API root, lists available routes   |
| GET    | `/temp`   | Returns current temperature (HTML) |
| POST   | `/led`    | Sets LED ON (1) or OFF (0)         |
| GET    | `/status` | Returns JSON with temp + LED state (assignment) |

### POST `/led` Body Formats
- JSON: `{"state": 1}`
- Form: `state=1`
- Plain text: `1`

### File Responsibilities
- `web_server.py` — HTTP parsing, routing, response building
- `main.py` — Wi-Fi connect, socket server loop, LED state application

### Assignment Addition: `GET /status`
- Returns `Content-Type: application/json`
- Body: `{"temperature": 24.55, "led_on": true}`
- Requires passing current LED state into `serve_client()` as a third argument

### Key Code Facts
- Pico W listens on **port 80**
- Temperature sensor is on `ADC(4)`
- `serve_client()` returns `-1` (no change), `0` (LED off), or `1` (LED on)

---

## Lab 2 — MQTT on Pico W

### Core Concepts
- **MQTT** = Message Queue Telemetry Transport — lightweight pub/sub protocol for IoT
- **Broker**: routes messages between clients (e.g. Mosquitto, default port **1883**)
- **Client**: publishes and/or subscribes
- **Topic**: hierarchical routing string (e.g. `csc2106/led/cmd`)

### QoS Levels
| Level | Description |
|-------|-------------|
| QoS 0 | Fire and forget — no acknowledgement |
| QoS 1 | At least once — ACK required from broker |
| QoS 2 | Exactly once — heaviest, not commonly used in embedded |

**In this lab: QoS 1 is used for all publishes/subscribes.**

### Last Will and Testament (LWT)
- Published **by the broker** on behalf of a client that **disconnects unexpectedly**
- Used to signal `"offline"` status to other clients
- Configured at connect time

### Retained Messages
- Stored by the broker
- Immediately delivered to **any new subscriber** on that topic
- Used for **state** topics (e.g. `online`/`offline` status)
- **NOT used for event topics** (e.g. ACK, commands)

### Pico A vs Pico B

| Aspect         | Pico A (Publisher)       | Pico B (Subscriber + Publisher)     |
|----------------|--------------------------|-------------------------------------|
| Role           | Input device             | Always-on actuator                  |
| Triggers       | Button press (GP21/22)   | Continuous listen                   |
| Publishes to   | `csc2106/led/cmd`, `csc2106/led/hello`, `csc2106/devA/status` | `csc2106/led/ack`, `csc2106/devB/status` |
| Subscribes to  | Nothing (base lab)       | `csc2106/led/cmd`                   |
| LWT            | Yes (`"offline"` on `devA/status`) | Yes (`"offline"` on `devB/status`) |

### State vs Event Topics
- **State** (use retained): `devA/status`, `devB/status`
- **Event** (do NOT retain): `led/cmd`, `led/ack`, `led/hello`

### Assignment: Bidirectional MQTT
- Both nodes publish AND subscribe
- Node A: press button → publish to `csc2106/nodeB/led/cmd` → Node B toggles LED
- Node B: press button → publish to `csc2106/nodeA/led/cmd` → Node A toggles LED
- Library used: `umqtt.simple` (installed in `lib/umqtt/simple.py` on Pico)

### Mosquitto Config (v2)
```
listener 1883
allow_anonymous true
```

---

## Lab 3 — BLE Flooding Mesh (Wireless Mesh)

### What it is
- NOT Bluetooth SIG BLE Mesh
- Application-layer flooding relay using **BLE advertisements** (broadcast) + **scanning** (receive)
- Simulates mesh networking without connections

### Frame Format
```
M1|ORIG|MSGID|TTL|TYPE|DATA
```
Example: `M1|a1b2c3|12345678|3|T|26.73`

| Field  | Meaning |
|--------|---------|
| `M1`   | Protocol identifier |
| `ORIG` | Original sender's node ID (last 6 hex chars of MAC) |
| `MSGID`| Unique message ID (timestamp-based) |
| `TTL`  | Time-To-Live (hop limit) |
| `TYPE` | `T` = temperature, `R` = response (assignment) |
| `DATA` | Payload (e.g. temp value) |

### Key Terms
- **Inject**: originate a new message (new MSGID, ORIG = self)
- **Advertise burst**: transmit for a short window (300 ms) then stop
- **Forward**: re-advertise a received message from someone else

### Lab Parts

| Part | Behaviour |
|------|-----------|
| 1 | Inject temp every ~60s + jitter, print received frames. No forwarding. |
| 2 | Forward all received frames. No dedup, no TTL → ping-pong / broadcast storm. |
| 3 | Add **de-duplication** (seen cache) + **TTL decrement** on forward. Stable flooding. |

### Part 3 Controls (Minimum for Stability)
1. **De-duplication**: keyed on `ORIG:MSGID`. Drop if already seen.
2. **TTL**: decrement on each forward. Drop if TTL reaches 0.
3. **Self-injection**: mark your own injected message as seen immediately (or you'll re-forward it).

### Why Part 2 Fails
- No memory → same message forwarded infinitely
- With only 2 nodes: packet bounces back and forth (ping-pong)

### Assignment: Event-Driven Injection
- After receiving **5 new messages** from OTHER nodes:
  1. Print details of all 5
  2. Inject a new message with `TYPE='R'`, `TTL=0`
  3. Reset counter
- `R` messages: **must NOT be forwarded**, **must NOT be counted** toward the 5
- "New" = passes dedup AND is not from self

### Config Values
- `ADV_BURST_MS = 300` — advertise for 300 ms per inject/forward
- `INJECT_PERIOD_S = 60` — inject every ~60 seconds
- `DEFAULT_TTL = 3` — max 3 hops
- `SEEN_MAX = 400` — max dedup cache size

---

## Lab 4 — LoRa

### What is LoRa?
- **LoRa** = Long Range radio modulation technique
- Designed for **low-power**, **long-distance** IoT communication
- Range: up to **16 km** in rural areas
- Operates in **license-free ISM band**
- Uses **lower frequencies** than Wi-Fi/BT → better penetration and range

### Topology
- **Peer-to-peer** (P2P) — direct radio communication between nodes
- Can scale to star/mesh topologies (LoRaWAN uses star-of-stars)

### Hardware Used
- Maker UNO + Cytron LoRa-RFM Shield
- Library: **RadioHead** by Paul Stoffregen
- Board setting: Arduino Uno

### Files
- `rf95_tx.ino` — Transmitter
- `rf95_rx.ino` — Receiver
- `ssd1306_i2c.ino` / `ssd1306_ascii.ino` — OLED display helpers

### Lab Assignment Tasks
1. **OLED Integration** — display status messages: "Setup Successful", "Setup Failed", "Sending Message", "Waiting for Reply", "Message Received"
2. **Reliability** — implement ACK + retransmit to prevent crosstalk
3. **Message Protocol** — ID-based messaging with header, payload, checksum; simple filtering (only accept messages directed to node's ID); support at least 3 devices

### LoRa vs Wi-Fi/BT
| Feature     | LoRa         | Wi-Fi/BT     |
|-------------|--------------|--------------|
| Range       | Up to 16 km  | ~100 m max   |
| Power       | Very low     | Higher       |
| Data rate   | Low (Kbps)   | High (Mbps)  |
| Frequency   | Sub-GHz ISM  | 2.4/5 GHz    |
| License     | Free ISM     | Free ISM     |

---

## Lab 6 — BLE Point-to-Point (GATT)

### Roles
- **Peripheral / Server** (Pico W 1): hosts GATT services, advertises, accepts connections
- **Central / Client** (Pico W 2): scans, connects, reads/writes characteristics

### GATT Concepts
- **GATT** = Generic Attribute Profile — defines how data is structured over BLE
- **Service**: a collection of related characteristics
- **Characteristic**: a data value with permissions (READ, WRITE, NOTIFY)
- **UUID**: unique identifier for services/characteristics

### Characteristics in This Lab

| Characteristic | UUID   | Permissions             | Role                          |
|----------------|--------|-------------------------|-------------------------------|
| LED State      | 0x1A00 | READ, WRITE             | Client reads/writes LED       |
| Status (Temp)  | 0x1A01 | READ, NOTIFY            | Server pushes temp/volt updates |
| Service        | 0x1A02 | (service UUID)          | Groups the two characteristics |

### BLE IRQ Events (Key ones)

| Event ID | Name                    | Who uses it  |
|----------|-------------------------|--------------|
| 1        | `CENTRAL_CONNECT`       | Server       |
| 2        | `CENTRAL_DISCONNECT`    | Server       |
| 3        | `GATTS_WRITE`           | Server       |
| 5        | `SCAN_RESULT`           | Client/Mesh  |
| 6        | `SCAN_DONE`             | Client/Mesh  |
| 15       | `GATTC_READ_DONE`       | Client       |
| 18       | `GATTC_NOTIFY`          | Client       |

### Tasks
| Task | Action                           | Server behaviour                        | Client behaviour                       |
|------|----------------------------------|-----------------------------------------|----------------------------------------|
| 1    | Setup                            | Registers characteristics, advertises   | Scans, connects, discovers             |
| 2    | Server Notify (button press)     | Toggles LED, calls `gatts_notify()`     | Receives `GATTC_NOTIFY`, prints status |
| 3    | Client Read (Button A / GP0)     | Serves read request                     | Calls `gattc_read()`, prints LED state |
| 4    | Client Write (Button B / GP1)    | Receives `GATTS_WRITE`, updates LED     | Calls `gattc_write()` with new state   |

### BLE Beacon Mode
- **Advertising-only** — never connects
- Transmits custom payload at regular intervals
- Most energy-efficient BLE mode
- Used for: location signaling, presence detection
- Difference from GATT Peripheral: **no services registered**, no connection handling
- iBeacon fields: UUID (16 bytes), Major (2 bytes), Minor (2 bytes), TX Power (1 byte)
- Verified with: **nRF Connect** or **BLE Scanner** mobile app

### Key Difference: Peripheral vs Beacon
| Feature              | GATT Peripheral         | BLE Beacon              |
|----------------------|-------------------------|-------------------------|
| Connections          | Yes                     | No                      |
| Services             | Registered              | None                    |
| Power use            | Higher                  | Lowest                  |
| Data delivery        | On demand (read/notify) | Broadcast only          |

---

## Cross-Topic Comparisons

### Protocol Comparison

| Feature         | REST         | MQTT             | BLE Mesh      | LoRa         | BLE P2P       |
|-----------------|--------------|------------------|---------------|--------------|---------------|
| Topology        | Client-server| Pub/sub via broker| Broadcast/flood| P2P / star  | P2P connected |
| Range           | Wi-Fi (~100m)| Wi-Fi (~100m)    | ~10 m (BLE)   | Up to 16 km  | ~10 m (BLE)   |
| Power           | Medium       | Low-medium       | Low           | Very low     | Low           |
| Reliability     | HTTP status codes | QoS 0/1/2  | TTL + dedup   | ACK/retransmit | GATT ACK   |
| Real-time push  | No (poll)    | Yes (NOTIFY/LWT) | Broadcast     | No           | Yes (NOTIFY)  |

### When is retained used in MQTT?
- **Yes**: status topics (`online`/`offline`) — represents **current state**
- **No**: event topics (`cmd`, `ack`) — represents **one-time events**

### When is TTL needed?
- Any flooding/broadcast mesh — without TTL, messages loop forever

---
