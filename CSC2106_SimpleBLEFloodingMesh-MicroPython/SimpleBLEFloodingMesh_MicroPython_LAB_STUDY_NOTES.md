# Simple BLE Flooding Mesh Lab Study Notes

## 1. Lab Overview
This is a mesh-like flooding demo built on BLE advertising + scanning in MicroPython.
It is not official Bluetooth SIG Mesh; flooding logic is implemented at application level.

## 2. Core Terms
- Inject: originate a new frame from this node.
- Forward: rebroadcast frame received from another node.
- Advertise burst: short transmit window (e.g., 300 ms) then stop.
- De-duplication: drop already-seen `(ORIG, MSGID)` packets.
- TTL: hop limit reduced on each forward.

## 3. Frame Format
`M1|ORIG|MSGID|TTL|TYPE|DATA`

Example:
`M1|a1b2c3|12345678|3|T|26.73`

Field purpose:
- `ORIG`: source node ID
- `MSGID`: unique message identifier
- `TTL`: remaining hops
- `TYPE`: message type (`T`, `R`, etc.)
- `DATA`: payload

## 4. Node Components
- BLE scan receives advertising payloads.
- BLE advertise sends short bursts.
- Parser validates and extracts frame fields.
- Seen cache prevents duplicate processing.
- TTL logic bounds propagation distance.

## 5. Part Progression
### Part 1
- Periodic own injection (temperature)
- Receive and print frames
- No forwarding

### Part 2
- Forward all received frames
- No dedupe/TTL control (intentionally flawed)
- Can cause storms and ping-pong

### Part 3
- Add dedupe cache and TTL decrement
- Forward only if packet is new and TTL > 0
- Mark own injected packets as seen to avoid bounce loops

### Assignment extension
- Event-driven trigger: after 5 new non-self non-`R` messages, inject one `R` message
- `R` has TTL 0 and is not forwarded or counted

## 6. Why Dedupe + TTL Are Essential
Without controls:
- Message rebroadcast loops persist.
- Network airtime gets saturated.

With controls:
- Each message has bounded lifetime.
- Each node processes each logical message once.
- Flooding remains observable without uncontrolled growth.

## 7. Assignment Logic Details
Count only packets that are:
- New (pass dedupe)
- Not from self (`orig != NODE_ID`)
- Not type `R`

When count reaches 5:
1. Print the 5 message details.
2. Inject `R` frame with TTL 0.
3. Clear buffer and restart count.

## 8. Common Bugs
- Not adding own injected `(ORIG, MSGID)` into seen cache.
- Forwarding `R` packets (assignment says no).
- Counting duplicate packets toward trigger.
- Forgetting to restart scan on scan-done IRQ.
- Failing to stop advertise burst after timeout.

## 9. Debug Checklist
1. Confirm node prints Node ID at start.
2. Confirm periodic `INJECT (T)` appears.
3. Confirm `RX NEW` appears from other nodes.
4. Confirm duplicates are dropped after seen-cache check.
5. Confirm forwarded packets show TTL decrement.
6. Confirm `R` injection occurs only on 5 valid received packets.
7. Confirm `R` frames are not forwarded.

## 10. Viva One-Liners
- Flooding improves reach but needs loop controls (dedupe + TTL).
- `(ORIG, MSGID)` is logical packet identity.
- TTL prevents infinite propagation.
- Event-driven injection models reactive IoT behavior.
- This lab mimics mesh-like relaying but is not BLE Mesh standard.
