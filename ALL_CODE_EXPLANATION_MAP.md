# All Code Explanation Map

This is a zero-prior-knowledge map of every code file in this repo.
Use it with MEGA_CODE_UNDERSTANDING_QBANK.md.

## BLE
- CSC2106_BLE-PicoW-MicroPython/assignment/ble_server.py
  - What: Peripheral/server with GATT service + 2 characteristics.
  - Does: advertises, handles connect/disconnect/write IRQ, periodically updates status and notifies clients.
  - Why: demonstrates event-driven GATTS and server push (notify).
- CSC2106_BLE-PicoW-MicroPython/assignment/ble_client.py
  - What: Central/client that scans and connects to server.
  - Does: discovers service/char/descriptors, writes CCCD, handles read/write/notify paths with button actions.
  - Why: demonstrates full GATTC workflow and runtime handle discovery.

## REST
- CSC2106_REST-PicoW-MicroPython/assignment/main.py
  - What: system orchestrator.
  - Does: Wi-Fi connect, socket accept loop, temperature read, calls route handler, applies LED state.
  - Why: keeps hardware control in one place.
- CSC2106_REST-PicoW-MicroPython/assignment/web_server.py
  - What: HTTP parser + router.
  - Does: parses request, routes endpoints, validates body formats, returns status codes + response body.
  - Why: separates protocol logic from hardware loop.

## MQTT
- CSC2106_MQTT-PicoW-MicroPython/picoA.py
  - What: publisher node.
  - Does: publishes command/hello on button edges, handles reconnect, retained online status + LWT offline.
  - Why: event publisher model.
- CSC2106_MQTT-PicoW-MicroPython/picoB.py
  - What: subscriber actuator node.
  - Does: check_msg polling, callback toggles LED on command topic, publishes ACK/status.
  - Why: callback-based subscriber behavior.
- CSC2106_MQTT-PicoW-MicroPython/simple.py
  - What: MQTT client library implementation.
  - Does: connect/subscribe/publish/check_msg internals, QoS handling basics.
  - Why: protocol engine under app code.
- CSC2106_MQTT-PicoW-MicroPython/assignment/nodeA.py
  - What: bidirectional node A.
  - Does: publishes to nodeB command topic and subscribes to nodeA command topic.
  - Why: two-way control topology.
- CSC2106_MQTT-PicoW-MicroPython/assignment/nodeB.py
  - What: bidirectional node B (mirror of nodeA).
  - Does: reverse topic directions relative to nodeA.
  - Why: symmetric pub+sub pair.
- CSC2106_MQTT-PicoW-MicroPython/assignment/simple.py
  - What: assignment-local copy of MQTT library.
  - Does: same role as top-level simple.py.
  - Why: self-contained assignment folder.

## BLE Flooding Mesh
- CSC2106_SimpleBLEFloodingMesh-MicroPython/main.py
  - What: base flooding mesh node.
  - Does: periodic inject, parse scan payload, dedupe by ORIG+MSGID, TTL-based forwarding, advertise burst timing.
  - Why: demonstrates controlled flooding.
- CSC2106_SimpleBLEFloodingMesh-MicroPython/assignment/mesh_node.py
  - What: enhanced assignment node.
  - Does: adds event trigger buffer and R-message injection logic on threshold.
  - Why: demonstrates reactive logic on received traffic.

## LoRa
- CSC2106_LoRa/rf95_tx.ino
  - What: baseline transmitter.
  - Does: send packet, wait sent, wait for reply with timeout, print RSSI.
  - Why: basic reliable send-receive loop.
- CSC2106_LoRa/rf95_rx.ino
  - What: baseline receiver.
  - Does: receive packet, print content + RSSI, send reply.
  - Why: basic RX/ACK side.
- CSC2106_LoRa/ssd1306_i2c.ino
  - What: OLED test via Adafruit library.
  - Does: initialize display, print status text.
  - Why: local visual debugging.
- CSC2106_LoRa/ssd1306_ascii.ino
  - What: OLED test via SSD1306Ascii library.
  - Does: lightweight text display path.
  - Why: lower-memory alternative.
- CSC2106_LoRa/assignment/tx.ino
  - What: enhanced transmitter with protocol struct.
  - Does: builds DATA packet (nodeID/destID/type/len/payload/checksum), retries on timeout, validates ACK logic, shows OLED status.
  - Why: assignment reliability and addressing requirements.
- CSC2106_LoRa/assignment/rx.ino
  - What: enhanced receiver with parser/validator.
  - Does: parse struct safely, validate checksum/destination, send ACK or NACK, OLED updates.
  - Why: robust receiver behavior in noisy channel.

## Study Order For Full Understanding
1. REST main.py + web_server.py
2. MQTT picoA.py + picoB.py
3. LoRa rf95_tx.ino + rf95_rx.ino
4. BLE ble_server.py + ble_client.py
5. Mesh main.py + assignment/mesh_node.py
6. Assignment-level enhanced files + MQTT simple.py internals
