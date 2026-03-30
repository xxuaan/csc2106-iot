# BLE Pico W Lab Study Notes

## 1. Lab Overview
This lab implements Bluetooth Low Energy communication between two Raspberry Pi Pico W boards.
- Server board runs as BLE Peripheral and hosts GATT data.
- Client board runs as BLE Central and scans, connects, reads, writes, and receives notifications.

You also study Beacon mode, where a node only advertises and does not accept GATT connections.

## 2. BLE Roles
- Peripheral (Server): advertises and exposes services/characteristics.
- Central (Client): scans and initiates connection.

In this lab:
- Server: assignment/ble_server.py
- Client: assignment/ble_client.py

## 3. GATT Structure Used in This Lab
### Service UUID
- 0x1A02

### Characteristics
- LED characteristic: UUID 0x1A00
  - Purpose: read and write server LED state
  - Data format: text bytes "0" or "1"
- Status characteristic: UUID 0x1A01
  - Purpose: read and notify status updates
  - Data format: status string such as temperature and voltage summary

## 4. Server Flow
1. Initialize BLE and register IRQ handler.
2. Register one custom service with two characteristics.
3. Start advertising with name and service UUID.
4. On client connect: store connection handle.
5. On client disconnect: remove handle and re-advertise.
6. On GATTS write to LED characteristic: parse value and update physical LED.
7. Periodically update status and optionally notify connected clients.

## 5. Client Flow
1. Start scan for target advertising name.
2. Stop scan when found and connect.
3. Discover service range.
4. Discover characteristics and store value handles.
5. Discover descriptors and find CCCD (0x2902).
6. Write 0x0100 to CCCD to enable notifications.
7. Runtime actions:
   - Button A: read LED from server (Task 3)
   - Button B: write LED to server (Task 4)
   - Notify callback: print status pushed by server (Task 2)

## 6. Why CCCD Matters
Notifications are disabled by default.
The client must write 0x0100 to the characteristic's CCCD to subscribe.
Without this, read and write can still work but notify events do not arrive.

## 7. Important BLE Event Concepts
BLE in MicroPython is event driven.
You react to IRQ events rather than assuming linear synchronous behavior.

Common event categories in this lab:
- Scan result and scan done
- Connect and disconnect
- Service/characteristic/descriptor discovery results and completion
- Read result and read done
- Write done
- Notify received

## 8. Typical Failure Cases
- Device name mismatch between server and client target filter.
- UUID mismatch between service or characteristics.
- Characteristic handles not discovered correctly.
- CCCD not found or not written successfully.
- Wrong assumptions about handle positions.
- No button debouncing causing repeated writes.
- Parsing failure for malformed write payload.

## 9. Debug Checklist
1. Confirm server is advertising and visible.
2. Confirm client scan finds correct target name.
3. Confirm service UUID discovery success.
4. Confirm LED and status handles are populated.
5. Confirm CCCD write returns success status.
6. Confirm notify handler compares correct value handle.
7. Add event logs to isolate exactly where flow breaks.

## 10. Beacon Mode Summary
Beacon mode is advertising only.
- No GATT connection lifecycle.
- No service/characteristic interactions.
- Optimized for low-power presence signaling and identification.

## 11. Viva Ready One-Liners
- Notify is server push; read is client pull.
- Service groups characteristics; characteristics carry values.
- CCCD controls notification subscription per connection.
- Re-advertise after disconnect to remain discoverable.
- Runtime handle discovery is safer than hardcoding handles.

## Code Focus

### Actual code files to master
- assignment/ble_server.py
- assignment/ble_client.py

### Server code checkpoints
- Verify UUID constants and service registration map to LED and status characteristics.
- Understand the IRQ branches for connect, disconnect, and write handling.
- Trace update_status flow: write characteristic values, then notify connected clients.
- Explain how advertising payload is built and why name filtering works on client side.

### Client code checkpoints
- Trace scan result -> stop scan -> connect -> discover service -> discover characteristics -> discover descriptors -> write CCCD.
- Explain handle usage for read/write/notify and why discovered handles are required.
- Explain button mapping and debounce logic in main loop.
- Differentiate READ_RESULT/READ_DONE and how notify callback is filtered by status handle.

### Code-level test prompts
- What fails first if CCCD write is skipped?
- What changes are needed to support two simultaneous clients cleanly?
- What error path should handle malformed LED write payload?

## In-Depth Code Walkthrough

### 1) Server GATT registration and handle mapping
```python
_LED_CHAR = (_LED_UUID, bluetooth.FLAG_READ | bluetooth.FLAG_WRITE | bluetooth.FLAG_WRITE_NO_RESPONSE,)
_STATUS_CHAR = (_STATUS_UUID, bluetooth.FLAG_READ | bluetooth.FLAG_NOTIFY,)
_SERVICE = (_SVC_UUID, (_LED_CHAR, _STATUS_CHAR,),)

self._handles = self._ble.gatts_register_services((_SERVICE,))
self._led_handle = self._handles[0][0]
self._status_handle = self._handles[0][1]
```
Why this matters:
- This defines one service with two characteristics and stores concrete runtime handles.
- All future read/write/notify operations depend on these handles being correct.

### 2) Server write IRQ path (client controls LED)
```python
elif event == _IRQ_GATTS_WRITE:
  conn_handle, value_handle = data
  if value_handle == self._led_handle:
    data_rx = self._ble.gatts_read(value_handle)
    new_state = int(data_rx.decode())
    onboard_led.value(new_state)
    self._led_state = new_state
```
Why this matters:
- This is the actuator control path for Task 4.
- Parse safety should be added here to avoid crashes on malformed payloads.

### 3) Client notify subscription via CCCD
```python
if uuid == bluetooth.UUID(0x2902):
  self._cccd_handle = dsc_handle

self._ble.gattc_write(conn_handle, self._cccd_handle, b'\x01\x00', 0)
```
Why this matters:
- Without this write, notify callbacks never fire even if server sends notifications.
- `0x0100` enables notification mode for that connection.

### 4) Read flow and result handling
```python
def read_led(self):
  if self._conn_handle is not None and self._led_handle is not None:
    self._ble.gattc_read(self._conn_handle, self._led_handle)

elif event == _IRQ_GATTC_READ_RESULT:
  conn_handle, value_handle, value = data
  led_state = int(bytes(value).decode().strip())
```
Why this matters:
- Read is asynchronous: request now, value arrives later via IRQ event.
- Good exam answer: distinguish request call vs callback payload path.

### 5) Debug strategy from code
- If connect works but no notify: inspect descriptor discovery and CCCD write status.
- If write appears successful but server LED unchanged: check client write handle and server write IRQ branch.
- If client never connects: verify advertised name string and scan filter target match.
