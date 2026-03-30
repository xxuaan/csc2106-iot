# LoRa Lab Study Notes

## 1. Lab Overview
This lab uses Maker UNO + Cytron LoRa-RFM Shield to build peer-to-peer LoRa communication.
- Transmitter sends payloads over RF95.
- Receiver listens, validates, displays, and sends ACK/NACK.
- Assignment extends baseline with OLED status, reliability, and simple addressing.

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
