# MQTT Pico W Lab Study Notes

## 1. Lab Overview
This lab implements MQTT publish-subscribe communication with Raspberry Pi Pico W nodes.
- Broker: Mosquitto on laptop/Raspberry Pi (port 1883)
- Clients: Pico W boards using `umqtt.simple`
- Core topics include control (`.../led/cmd`), acknowledgements (`.../led/ack`), and status (`.../status`)

## Explain It Fast: What / Does / Why

### MQTT Broker
- What is this: central message router for all MQTT clients.
- What it does: receives published messages and forwards them to matching subscribers.
- Why it is done: decouples devices so publishers do not need direct socket links to each consumer.

### Topic-Based Pub/Sub
- What is this: string-based routing model (for example, `csc2106/nodeA/led/cmd`).
- What it does: lets different message types flow independently through dedicated channels.
- Why it is done: scalable organization and easier filtering/debugging.

### QoS 1
- What is this: at-least-once delivery mode.
- What it does: broker acknowledgement improves delivery confidence but may produce duplicates.
- Why it is done: better reliability than QoS 0 on unstable Wi-Fi links.

### LWT (Last Will)
- What is this: fallback message broker publishes when client drops unexpectedly.
- What it does: marks node as `offline` without graceful shutdown code path.
- Why it is done: health visibility for monitoring clients.

### Retained Messages
- What is this: broker-stored last value on a topic.
- What it does: immediately sends latest state to new subscribers.
- Why it is done: late joiners get current system state without waiting for next publish.

### check_msg Polling
- What is this: non-blocking inbound message pump in `umqtt.simple`.
- What it does: triggers callback execution for subscribed topics.
- Why it is done: this client has no background thread; loop polling is mandatory.

## Beginner Deep Dive (If You Are New)

### Mental model
Think of MQTT like a post office:
- Devices do not send directly to each other.
- They send to broker (post office) with a topic label (address).
- Anyone subscribed to that topic receives the message.

### End-to-end flow for one command
1. Node A connects to broker and publishes `TOGGLE` to Node B command topic.
2. Broker checks subscriptions and forwards to Node B.
3. Node B callback runs, validates topic/payload, toggles LED.
4. Node B optionally publishes ACK/status.

### Why design choices matter
- Separate command and status topics: avoids mixing events with state snapshots.
- Retained status: late subscribers immediately know current online/offline state.
- Non-retained command: prevents stale commands replaying after reboot.
- QoS 1: increases reliability on unstable Wi-Fi, with duplicate risk.

### Beginner mistakes and how to avoid them
- Mistake: topic typo by one path segment.
  Fix: keep topic constants centralized and shared.
- Mistake: forgetting re-subscribe after reconnect.
  Fix: put subscribe calls in reconnect/setup function.
- Mistake: callback set but `check_msg()` not called.
  Fix: call `check_msg()` frequently in main loop.
- Mistake: using same client ID on two devices.
  Fix: ensure unique client ID per device.

### Duplicate behavior intuition (QoS 1)
QoS 1 means at-least-once, not exactly-once.
- A duplicate command can arrive.
- Pure toggle commands can flip twice unexpectedly.
- If needed, add dedupe tokens or make handlers idempotent.

### Beginner Flow Diagram (Text)
```text
NODE A                      BROKER (Mosquitto)                    NODE B
Boot/connect Wi-Fi          Running/listening                     Boot/connect Wi-Fi
  |                               |                                   |
Connect MQTT --------------------->|<----------------------------- Connect MQTT
Set LWT, subscribe/check_msg loop  |      Set LWT, subscribe/check_msg loop
  |                               |                                   |
Publish TOGGLE cmd --------------->|-------------------------------> Receive in callback
  |                               |                                   |
Publish status online ------------>|-------------------------------> Subscribers get state
  |                               |                                   |
Unexpected drop                    | publishes LWT offline ----------> Subscribers see offline
```

Loop note:
- `check_msg()` must run regularly or callbacks will not fire.

## 2. Core MQTT Concepts
- Broker: routes messages between clients.
- Client: publisher, subscriber, or both.
- Topic: hierarchical routing string.
- QoS:
  - QoS 0: at most once
  - QoS 1: at least once (used in this lab)
  - QoS 2: exactly once (not used)
- LWT (Last Will and Testament): broker publishes offline status on unexpected disconnect.
- Retained message: broker stores last state and sends it immediately to new subscribers.

## 3. Architecture in This Repo
### Example pair
- `picoA.py`: event publisher from button(s), publishes commands/status
- `picoB.py`: subscriber actuator, toggles LED and publishes ACK/status

### Assignment pair
- `assignment/nodeA.py`: publishes `TOGGLE` to Node B and subscribes for commands to itself
- `assignment/nodeB.py`: symmetric role of Node A in opposite direction
- Both are pub+sub nodes for bidirectional control

## 4. Topic Design Pattern
- State topics (retained): `csc2106/devA/status`, `csc2106/devB/status`, `csc2106/nodeA/status`, `csc2106/nodeB/status`
- Event topics (not retained): `.../led/cmd`, `.../led/ack`, `.../led/hello`

Why:
- State should be instantly known by late joiners (retained).
- Events should not replay to new subscribers (not retained).

## 5. Node Runtime Flow
1. Connect to Wi-Fi (`network.WLAN(STA_IF)`).
2. Create MQTT client with keepalive.
3. Configure LWT to publish offline retained status.
4. Connect and publish online retained status.
5. Subscribe to control topic.
6. Loop:
   - Check button edges and publish commands.
   - Call `check_msg()` for incoming packets.
   - On callback, validate topic/message and toggle LED.
   - Reconnect on exceptions.

## 6. Important Reliability Choices
- QoS 1 on commands and key status messages.
- Reconnect strategy in publish/check loops.
- Keepalive to detect dead sessions.
- LWT for ungraceful disconnect visibility.

## 7. Assignment Requirements (Bidirectional Control)
- Node A button press publishes command to Node B topic.
- Node B subscribes and toggles LED on valid command.
- Node B button press publishes command to Node A topic.
- Node A subscribes and toggles LED on valid command.

## 8. Common Issues
- Wrong broker IP or unreachable network segment.
- Topic mismatch (publisher and subscriber strings differ).
- Missing `set_callback(...)` before `check_msg()` usage.
- Not subscribing after reconnect.
- No pull-up configuration on buttons causing noisy triggers.
- Retain misused on event topics causing unexpected replays.

## 9. Test Checklist
1. Verify both nodes connect and print Wi-Fi IP.
2. Verify each node publishes retained `online` status.
3. Press Node A button -> Node B LED toggles.
4. Press Node B button -> Node A LED toggles.
5. Stop one node abruptly -> other monitor sees retained `offline` via LWT topic.
6. Restart node -> status returns to `online` retained.

## 10. Viva One-Liners
- MQTT is decoupled by broker and topic, unlike direct socket pairings.
- QoS 1 may duplicate messages; handlers should be idempotent when needed.
- LWT gives failure visibility without explicit shutdown publish.
- Retained is for latest state, not transient events.
- `check_msg()` polling loop is central in `umqtt.simple` non-threaded flow.

## Code Focus

### Actual code files to master
- assignment/nodeA.py
- assignment/nodeB.py
- picoA.py
- picoB.py

### Connection and session checkpoints
- Trace Wi-Fi connect and MQTT client construction with keepalive.
- Explain set_last_will and retained online status publish sequence.
- Explain why subscriptions must be re-established after reconnect.

### Message-flow checkpoints
- NodeA button event publishes TOGGLE to NodeB command topic.
- NodeB callback validates topic/message and toggles LED.
- NodeB button event publishes back to NodeA topic for bidirectional control.
- check_msg polling loop drives callback execution in umqtt.simple.

### Code-level test prompts
- What breaks if callback is set after subscribe/connect flow?
- Why should command topics stay non-retained while status topics stay retained?
- How can QoS1 duplicates affect toggle semantics and how to guard against it?

## In-Depth Code Walkthrough

### 1) Robust MQTT session setup pattern
```python
c = MQTTClient(CLIENT_ID, BROKER, keepalive=30)
c.set_last_will(STATUS_TOPIC, b"offline", retain=True, qos=1)
c.connect()
c.set_callback(on_msg)
c.subscribe(CMD_TOPIC, qos=1)
c.publish(STATUS_TOPIC, b"online", retain=True, qos=1)
```
Why this matters:
- Encodes availability semantics: retained online + LWT offline.
- Critical order: connect, callback, subscribe, then steady-state loop.

### 2) Callback-driven actuator control
```python
def on_msg(topic, msg):
  if topic == CMD_TO_B_TOPIC and msg == b"TOGGLE":
    led.value(0 if led.value() else 1)
```
Why this matters:
- This is the core subscriber behavior in assignment nodes.
- Topic and payload checks prevent accidental actions from unrelated traffic.

### 3) Polling loop that drives callback execution
```python
while True:
  try:
    client.check_msg()
  except Exception:
    client = make_client()
  time.sleep(0.02)
```
Why this matters:
- `check_msg()` is mandatory in `umqtt.simple`; no background thread handles inbound packets.

### 4) Reconnect-safe publish helper
```python
def publish_toggle(client):
  try:
    client.publish(CMD_TO_A_TOPIC, b"TOGGLE", qos=1)
    return client
  except Exception:
    try:
      client.disconnect()
    except:
      pass
    return make_client()
```
Why this matters:
- Keeps node resilient during transient broker/network failures.
- Returns a fresh client reference when reconnection occurs.

### 5) Code-level design cautions
- QoS 1 is at-least-once, so duplicate message processing is possible.
- Retained command topics can replay stale actions; keep retain for status, not commands.
- Duplicate client IDs can force broker to drop one session.
