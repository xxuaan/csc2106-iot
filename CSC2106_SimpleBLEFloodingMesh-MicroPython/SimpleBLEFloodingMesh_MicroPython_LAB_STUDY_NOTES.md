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

## Code Focus

### Actual code file to master
- assignment/mesh_node.py

### Frame and parser checkpoints
- Understand frame layout M1|ORIG|MSGID|TTL|TYPE|DATA.
- Explain parse_frame validation and failure returns.
- Explain why frame_to_name truncation is required for advertising payload limits.

### Flooding control checkpoints
- seen_check_add de-duplication logic and SEEN_MAX trimming.
- forward_ttl behavior and TTL decrement boundary.
- Why own injected packet must be marked seen immediately.

### Assignment trigger checkpoints
- Count only new, non-self, non-R packets.
- On fifth packet: print buffer, inject R with TTL 0, reset buffer.
- Ensure R packets are not forwarded and not counted.

### Code-level test prompts
- Which line causes endless ping-pong if removed?
- What happens if scan restart on scan-done is missing?
- Why is event-driven trigger placed in RX path, not main loop timer?

## In-Depth Code Walkthrough

### 1) Frame build and parse contract
```python
def make_frame(orig, msgid, ttl, typ, data):
	return "M1|{}|{}|{}|{}|{}".format(orig, msgid, ttl, typ, data)

def parse_frame(s):
	if not s.startswith("M1|"):
		return None
	parts = s.split("|", 5)
	_, orig, msgid, ttl_s, typ, data = parts
	return orig, msgid, int(ttl_s), typ, data
```
Why this matters:
- Defines the protocol contract all nodes must interpret identically.
- Parser rejection is first defense against garbage advertisement payloads.

### 2) Burst advertising model
```python
def advertise_burst_start(self, frame, duration_ms=ADV_BURST_MS):
	payload = adv_payload_name(frame_to_name(frame))
	self.ble.gap_advertise(ADV_INTERVAL_US, adv_data=payload)
	self._adv_active = True
	self._adv_stop_ms = time.ticks_add(time.ticks_ms(), duration_ms)

def advertise_burst_service(self):
	if self._adv_active and time.ticks_diff(time.ticks_ms(), self._adv_stop_ms) >= 0:
		self.ble.gap_advertise(None)
		self._adv_active = False
```
Why this matters:
- Simulates discrete packet sending without continuous transmission.
- Main loop must call service function frequently or ads will not stop on time.

### 3) De-duplication core
```python
def seen_check_add(self, key):
	if key in self.seen:
		return True
	self.seen.append(key)
	if len(self.seen) > SEEN_MAX:
		del self.seen[0:len(self.seen) - SEEN_MAX]
	return False
```
Why this matters:
- Prevents reprocessing same logical packet forever.
- Size cap avoids unbounded memory growth.

### 4) Forwarding with TTL control
```python
def forward_ttl(self, orig, msgid, ttl, typ, payload):
	ttl2 = ttl - 1
	if ttl2 < 0:
		return
	fwd = make_frame(orig, msgid, ttl2, typ, payload)
	self.advertise_burst_start(fwd)
```
Why this matters:
- Ensures propagation is bounded by hop count.
- If TTL is not decremented, storms return quickly.

### 5) Assignment event trigger path
```python
if typ != "R" and orig != NODE_ID:
	self.rx_buffer.append((orig, msgid))
	if len(self.rx_buffer) == 5:
		self.inject_R()

if typ != "R" and ttl > 0:
	self.forward_ttl(orig, msgid, ttl, typ, payload)
```
Why this matters:
- Trigger logic and forwarding logic are intentionally separated.
- `R` packets are excluded from both count and forwarding by design.
