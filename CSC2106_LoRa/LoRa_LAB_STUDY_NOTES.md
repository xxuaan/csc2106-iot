# LoRa Lab Study Notes

## 1. Lab Overview
This lab uses Maker UNO + Cytron LoRa-RFM Shield to build peer-to-peer LoRa communication.
- Transmitter sends payloads over RF95.
- Receiver listens, validates, displays, and sends ACK/NACK.
- Assignment extends baseline with OLED status, reliability, and simple addressing.

## Explain It Fast: What / Does / Why

### LoRa P2P Link
- What is this: direct radio communication between nodes without LoRaWAN gateway/server.
- What it does: sends small payloads over long distance using low data rates.
- Why it is done: simple deployment and long-range IoT telemetry with low power.

### RH_RF95 (RadioHead)
- What is this: Arduino driver abstraction for the RF95 LoRa transceiver.
- What it does: initializes radio, sends packets, receives packets, and exposes RSSI.
- Why it is done: avoids low-level register programming and accelerates reliable implementation.

### ACK/NACK + Retry
- What is this: application-level reliability mechanism.
- What it does: receiver confirms success (ACK) or corruption (NACK); transmitter retries on timeout.
- Why it is done: radio channels are lossy; retries reduce effective packet loss.

### Checksum
- What is this: lightweight integrity check over header and payload.
- What it does: detects accidental corruption during transmission.
- Why it is done: avoids acting on damaged data and drives NACK branch.

### Destination ID Filtering
- What is this: packet addressing field checked by receiver.
- What it does: ignores packets meant for other nodes.
- Why it is done: cuts noise/crosstalk processing in multi-node environments.

### OLED Status Layer
- What is this: local runtime state display channel.
- What it does: shows setup/send/wait/receive outcomes without relying on serial monitor.
- Why it is done: faster physical debugging and demo visibility.

## Beginner Deep Dive (If You Are New)

### Mental model
Think of LoRa here like walkie-talkies with receipts:
- TX sends a message.
- RX checks whether message is meant for it and whether content is intact.
- RX sends back ACK (receipt) if valid, or NACK if corrupted.
- TX retries if no ACK arrives before timeout.

### What a full successful cycle looks like
1. TX builds packet: header fields + payload + checksum.
2. TX sends and waits until packet transmission is finished.
3. RX receives and parses fields safely.
4. RX validates destination ID and checksum.
5. RX sends ACK to TX.
6. TX receives ACK and stops retrying.

### Why each part exists
- Destination ID: prevents every node from processing every packet.
- Checksum: catches accidental corruption from radio noise.
- ACK timeout + retry: improves delivery under interference.
- `waitPacketSent()`: avoids timing races after send call.

### Beginner mistakes and how to avoid them
- Mistake: frequency mismatch between nodes.
	Fix: set identical frequency on TX and RX.
- Mistake: checksum function differs between TX and RX.
	Fix: keep field order and covered bytes exactly the same.
- Mistake: ACK sent to wrong destination ID.
	Fix: ACK target must be original sender ID.
- Mistake: treating serial print as proof ACK path works.
	Fix: verify TX actually receives ACK, not only RX receiving DATA.

### Practical tuning intuition
- Larger timeout: fewer false retries but slower failure detection.
- More retries: better reliability but higher airtime and latency.
- Better OLED/serial logs: faster diagnosis than guessing.

### Beginner Flow Diagram (Text)
```text
TX NODE                                      RX NODE
Boot/init RF95                                Boot/init RF95
  |                                              |
Build packet (ID, dest, type, payload, checksum) |
  |                                              |
Send packet  -----------------------------------> Receive packet
WaitPacketSent()                                  Parse + validate length
  |                                              |
Start ACK timer                                   Check destID + checksum
  |                                              |
If ACK received: success <---------------------- Send ACK
If ACK timeout: retry                             If checksum fail: send NACK
  |                                              |
Stop at max retries                               Show status on OLED/serial
```

## 2. LoRa Basics
- Long-range, low-power radio modulation.
- Operates in ISM bands.
- Good for low data-rate IoT telemetry over long distances.
- In this lab, communication is P2P (not LoRaWAN).

## 3. Stack and Libraries
- Radio driver: `RH_RF95` (RadioHead).
- Display: `Adafruit_SSD1306` + `Adafruit_GFX`.
- Transport in code: custom message struct and checksum.

## 4. Message Protocol in Assignment Code
Struct fields:
- `nodeID`: sender ID
- `destID`: destination ID (or broadcast)
- `msgType`: DATA, ACK, NACK
- `payloadLen`: payload byte length
- `payload[40]`: content
- `checksum`: XOR checksum over header+payload

Packet flow:
1. TX composes message and checksum.
2. TX sends packet.
3. RX checks destination and checksum.
4. RX sends ACK on success, NACK on checksum failure.
5. TX waits for ACK with timeout and retries if needed.

## 5. Reliability Features
- ACK-based confirmation.
- Retransmit on timeout up to max retry count.
- NACK on checksum failure.
- Message filtering by destination ID reduces crosstalk.

## 6. OLED Status Integration
Expected status phrases include:
- Setup Successful
- Setup Failed
- Sending Message
- Waiting for Reply
- Message Received

OLED helps confirm node state without serial monitor.

## 7. Key Parameters in Code
- Frequency: 915.5 MHz (must match on both nodes).
- ACK timeout: 10 seconds in sample assignment tx.
- Max retries: 3.
- Node IDs and destination IDs must be coordinated.

## 8. Common Failure Points
- Frequency mismatch between transmitter and receiver.
- Incorrect node destination IDs (messages ignored).
- OLED size mismatch (128x32 vs 128x64 config).
- Missing libraries in Arduino IDE.
- Bad checksum logic or payload length mismatch.
- Not waiting for packet send completion.

## 9. Test Checklist
1. Both nodes boot and show setup success.
2. TX sends payload with visible serial logs.
3. RX prints payload, sender ID, RSSI, checksum status.
4. RX sends ACK and TX receives ACK before timeout.
5. Corrupt packet case sends NACK (if simulated).
6. Wrong destination packet is ignored by non-target node.

## 10. Viva One-Liners
- ACK+retry improves delivery reliability in noisy channels.
- ID-based filtering avoids processing irrelevant traffic.
- Checksum detects corruption, not confidentiality.
- RSSI aids link-quality observation during testing.
- LoRa P2P here is not LoRaWAN network operation.

## Code Focus

### Actual code files to master
- assignment/tx.ino
- assignment/rx.ino

### Transmitter checkpoints
- Message struct field packing into byte buffer.
- Checksum generation over header and payload.
- ACK wait loop timing, retry count, and failure path.

### Receiver checkpoints
- Packet parse bounds checks and payload length validation.
- Destination ID filter handling (own ID or broadcast).
- Checksum verify branch: ACK on pass, NACK on fail.
- OLED status transitions tied to receive outcomes.

### Protocol checkpoints
- Explain how ID, msgType, payloadLen, payload, checksum work together.
- Explain why waitPacketSent is needed before moving on.
- Explain how ACK timeout tuning affects reliability/latency tradeoff.

### Code-level test prompts
- Why would TX keep retrying even when RX prints data?
- Which mismatch causes persistent packet ignore behavior?
- How do you verify checksum branch using serial logs?

## In-Depth Code Walkthrough

### 1) Message structure and checksum coupling
```cpp
struct Message {
	uint8_t nodeID;
	uint8_t destID;
	uint8_t msgType;
	uint8_t payloadLen;
	char payload[40];
	uint8_t checksum;
};
```
Why this matters:
- Payload length is explicit, so parse boundary checks are possible.
- Checksum must include exactly the same fields on TX and RX.

### 2) TX send path
```cpp
buf[len++] = msg->nodeID;
buf[len++] = msg->destID;
buf[len++] = msg->msgType;
buf[len++] = msg->payloadLen;
memcpy(&buf[len], msg->payload, msg->payloadLen);
len += msg->payloadLen;
buf[len++] = msg->checksum;

rf95.send(buf, len);
rf95.waitPacketSent();
```
Why this matters:
- Byte layout order defines protocol compatibility.
- `waitPacketSent()` ensures radio TX is completed before moving to ACK wait logic.

### 3) RX parse safety checks
```cpp
if (len < 5) return false;
msg->payloadLen = buf[idx++];
if (msg->payloadLen > 40) return false;
if (len < idx + msg->payloadLen + 1) return false;
```
Why this matters:
- Prevents buffer misuse on malformed or truncated packets.
- Important short-answer point: parser must reject invalid lengths early.

### 4) ACK/NACK branch logic
```cpp
if (calculatedChecksum == msg.checksum) {
	sendAck(msg.nodeID);
} else {
	sendNack(msg.nodeID);
}
```
Why this matters:
- Reliability loop depends on correct acknowledgment semantics.
- TX retry outcomes are directly tied to this branch.

### 5) TX retry loop pattern
```cpp
while (!ackReceived && retries < MAX_RETRIES) {
	sendMessage(&msg);
	ackReceived = waitForAck(ACK_TIMEOUT);
	if (!ackReceived) {
		retries++;
	}
}
```
Why this matters:
- This is the core reliability mechanism under packet loss.
- Timeout and retry count should be tuned to channel conditions.
