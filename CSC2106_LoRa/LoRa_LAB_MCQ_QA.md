# LoRa Lab MCQ Questions and Answers

## Section A: Core MCQs

<ol>
  <li>In this lab, communication topology is mainly:<br>
    A. LoRaWAN star with gateway<br>
    B. Peer-to-peer between nodes<br>
    C. BLE mesh<br>
    D. Wi-Fi AP mode
  </li>
  <li>Primary radio library used is:<br>
    A. PubSubClient<br>
    B. RH_RF95 (RadioHead)<br>
    C. TinyUSB<br>
    D. FastLED
  </li>
  <li>ACK in this protocol is used to:<br>
    A. Increase payload size<br>
    B. Confirm successful reception<br>
    C. Replace checksums<br>
    D. Set RF frequency
  </li>
  <li>If ACK is not received in timeout window, transmitter should:<br>
    A. Assume success<br>
    B. Retry transmission up to configured limit<br>
    C. Change destination ID randomly<br>
    D. Disable receiver
  </li>
  <li>Checksum in this assignment mainly provides:<br>
    A. Encryption<br>
    B. Corruption detection<br>
    C. Compression<br>
    D. Synchronization timing
  </li>
  <li>ID-based destination filtering helps to:<br>
    A. Reduce crosstalk/interference processing<br>
    B. Increase OLED contrast<br>
    C. Prevent retransmit<br>
    D. Improve USB speed
  </li>
  <li>In receiver logic, message for other destination should be:<br>
    A. Processed fully<br>
    B. Ignored<br>
    C. Converted to ACK<br>
    D. Broadcast again
  </li>
  <li>OLED status messages are useful because they:<br>
    A. Replace RF module<br>
    B. Provide immediate runtime state visibility<br>
    C. Increase RF range<br>
    D. Remove need for serial
  </li>
  <li>Both nodes must share same LoRa frequency because:<br>
    A. Different frequencies improve reliability<br>
    B. Radio link requires matched channel settings<br>
    C. Only transmitter uses frequency<br>
    D. Checksum depends on frequency
  </li>
  <li>RSSI displayed on receiver is mainly used for:<br>
    A. Payload encryption<br>
    B. Link quality observation<br>
    C. Checksum generation<br>
    D. OLED address detection
  </li>
  <li>Message type constants DATA/ACK/NACK are examples of:<br>
    A. Application-layer protocol fields<br>
    B. UART parity settings<br>
    C. SPI pin mapping<br>
    D. ADC channels
  </li>
  <li>A likely reason for no packets received despite TX sending is:<br>
    A. Same frequency on both nodes<br>
    B. Frequency mismatch or wiring issue<br>
    C. ACK enabled<br>
    D. Payload too short
  </li>
</ol>

### Answers: Section A
<ol>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="13">
  <li>Transmitter retries three times even though receiver prints message once each attempt. Most likely cause:<br>
    A. Receiver sends ACK to wrong destination ID<br>
    B. Payload too long for struct<br>
    C. OLED not initialized<br>
    D. SPI clock too low
  </li>
  <li>Receiver accepts packet but checksum fails often. Most probable bug area:<br>
    A. Inconsistent checksum computation over fields/payload length<br>
    B. Missing delay in setup<br>
    C. Wrong serial baud only<br>
    D. Node ID constants are hex
  </li>
  <li>Node receives many unrelated packets in crowded environment. Best protocol improvement already suggested by assignment:<br>
    A. Remove destination field<br>
    B. Use ID-based filtering with header and checksum<br>
    C. Increase OLED text size<br>
    D. Disable ACK
  </li>
  <li>Receiver sends ACK, but TX still times out occasionally under interference. Best practical adjustment:<br>
    A. Add retry with backoff and tune ACK timeout<br>
    B. Remove checksum<br>
    C. Switch ACK to retained topic<br>
    D. Use Wi-Fi instead
  </li>
  <li>OLED shows gibberish or nothing while radio works. Most likely display-side issue:<br>
    A. Wrong OLED dimensions/config (128x32 vs 128x64)<br>
    B. Wrong LoRa frequency<br>
    C. ACK timeout too long<br>
    D. Node ID mismatch
  </li>
  <li>Receiver should send NACK when:<br>
    A. Destination is different node<br>
    B. Checksum validation fails on message for this node<br>
    C. RSSI is low<br>
    D. Payload contains text only
  </li>
  <li>Two nodes share same nodeID accidentally. Likely effect:<br>
    A. Cleaner routing<br>
    B. Ambiguous addressing/ACK confusion<br>
    C. Better checksum reliability<br>
    D. No impact
  </li>
  <li>Why call `waitPacketSent()` after send?<br>
    A. Ensures packet transmission completes before next logic<br>
    B. Generates checksum<br>
    C. Clears OLED buffer<br>
    D. Enables receive interrupt
  </li>
</ol>

### Answers: Section B
<ol start="13">
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
</ol>

## Section C: Code-Trace MCQs + Short Answers

### C1. Code-Trace MCQs
<ol>
  <li>TX retries until max even though RX prints payload. Most likely mismatch is:<br>
    A. ACK destination/source fields not matching TX node ID expectation
    B. OLED text size
    C. RSSI too high
    D. SPI include order
  </li>
  <li>RX receives packet but labels checksum fail frequently. Best code area to inspect:
    A. Checksum function field order and payloadLen boundaries
    B. OLED reset pin
    C. Serial baud
    D. Delay duration
  </li>
  <li>Many irrelevant packets shown in crowded channel. Assignment-level mitigation is:
    A. Ignore destID checks
    B. ID-based filtering by destination
    C. Remove checksum
    D. Reduce payloadLen to zero
  </li>
  <li>TX often reports timeout in noisy environment. Most robust improvement is:<br>
    A. Add retry/backoff and tune ACK timeout
    B. Disable ACK
    C. Remove waitPacketSent
    D. Lower node IDs
  </li>
  <li>OLED blank but serial indicates radio works. Most likely issue is:<br>
    A. Display config mismatch or init failure handling path
    B. Frequency mismatch
    C. Wrong checksum
    D. ACK type constant
  </li>
</ol>

### Answers: C1
<ol>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
</ol>

### C2. Short-Answer Bank (Model Answers)
1. Question: Why include payloadLen in both packet and checksum logic?
Model answer: Receiver needs payload boundary for safe parse, and checksum must include length to detect truncation/length tampering.

2. Question: Why waitPacketSent is important after rf95.send?
Model answer: It blocks until radio transmission completes, preventing immediate logic transitions that could disrupt timing assumptions.

3. Question: Why is checksum alone not a security mechanism?
Model answer: It detects accidental corruption but does not authenticate sender or protect confidentiality against intentional tampering.

4. Question: Why can identical node IDs cause reliability issues?
Model answer: ACK/addressing decisions rely on node identity; duplicates create ambiguous routing and false ACK interpretation.

5. Question: Difference between ACK and NACK branch purpose?
Model answer: ACK confirms valid delivery/parse, NACK signals detected corruption so sender can retry with awareness of failure type.
