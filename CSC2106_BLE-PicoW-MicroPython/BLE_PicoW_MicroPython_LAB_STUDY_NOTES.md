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
