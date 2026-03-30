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
