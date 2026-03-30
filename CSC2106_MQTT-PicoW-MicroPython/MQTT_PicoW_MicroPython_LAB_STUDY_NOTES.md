# MQTT Pico W Lab Study Notes

## 1. Lab Overview
This lab implements MQTT publish-subscribe communication with Raspberry Pi Pico W nodes.
- Broker: Mosquitto on laptop/Raspberry Pi (port 1883)
- Clients: Pico W boards using `umqtt.simple`
- Core topics include control (`.../led/cmd`), acknowledgements (`.../led/ack`), and status (`.../status`)

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
