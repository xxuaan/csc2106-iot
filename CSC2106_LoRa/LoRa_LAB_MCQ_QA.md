

> For a much larger code-focused question set, use [MEGA_CODE_UNDERSTANDING_QBANK.md](../MEGA_CODE_UNDERSTANDING_QBANK.md) or the root file in this repo.

## Lab Test Format Alignment
- Focus: no coding required.
- Question type: MCQ / multi-select style conceptual understanding.
- Count: 20 questions (practice set).

## Section A: Lab-Test Style Questions (20)

<ol>
  <li>In this lab, communication topology is mainly:<br>
      A. LoRaWAN star with gateway<br>
      B. Peer-to-peer between nodes<br>
      C. BLE mesh<br>
      D. Wi-Fi AP mode<br>
    </li>
  <li>Primary radio library used is:<br>
      A. PubSubClient<br>
      B. RH_RF95 (RadioHead)<br>
      C. TinyUSB<br>
      D. FastLED<br>
    </li>
  <li>ACK in this protocol is used to:<br>
      A. Increase payload size<br>
      B. Confirm successful reception<br>
      C. Replace checksums<br>
      D. Set RF frequency<br>
    </li>
  <li>If ACK is not received in timeout window, transmitter should:<br>
      A. Assume success<br>
      B. Retry transmission up to configured limit<br>
      C. Change destination ID randomly<br>
      D. Disable receiver<br>
    </li>
  <li>Checksum in this assignment mainly provides:<br>
      A. Encryption<br>
      B. Corruption detection<br>
      C. Compression<br>
      D. Synchronization timing<br>
    </li>
  <li>ID-based destination filtering helps to:<br>
      A. Reduce crosstalk/interference processing<br>
      B. Increase OLED contrast<br>
      C. Prevent retransmit<br>
      D. Improve USB speed<br>
    </li>
  <li>In receiver logic, message for other destination should be:<br>
      A. Processed fully<br>
      B. Ignored<br>
      C. Converted to ACK<br>
      D. Broadcast again<br>
    </li>
  <li>OLED status messages are useful because they:<br>
      A. Replace RF module<br>
      B. Provide immediate runtime state visibility<br>
      C. Increase RF range<br>
      D. Remove need for serial<br>
    </li>
  <li>Both nodes must share same LoRa frequency because:<br>
      A. Different frequencies improve reliability<br>
      B. Radio link requires matched channel settings<br>
      C. Only transmitter uses frequency<br>
      D. Checksum depends on frequency<br>
    </li>
  <li>RSSI displayed on receiver is mainly used for:<br>
      A. Payload encryption<br>
      B. Link quality observation<br>
      C. Checksum generation<br>
      D. OLED address detection<br>
    </li>
  <li>Message type constants DATA/ACK/NACK are examples of:<br>
      A. Application-layer protocol fields<br>
      B. UART parity settings<br>
      C. SPI pin mapping<br>
      D. ADC channels<br>
    </li>
  <li>A likely reason for no packets received despite TX sending is:<br>
      A. Same frequency on both nodes<br>
      B. Frequency mismatch or wiring issue<br>
      C. ACK enabled<br>
      D. Payload too short<br>
    </li>
  <li>Transmitter retries three times even though receiver prints message once each attempt. Most likely cause:<br>
      A. Receiver sends ACK to wrong destination ID<br>
      B. Payload too long for struct<br>
      C. OLED not initialized<br>
      D. SPI clock too low<br>
    </li>
  <li>Receiver accepts packet but checksum fails often. Most probable bug area:<br>
      A. Inconsistent checksum computation over fields/payload length<br>
      B. Missing delay in setup<br>
      C. Wrong serial baud only<br>
      D. Node ID constants are hex<br>
    </li>
  <li>Node receives many unrelated packets in crowded environment. Best protocol improvement already suggested by assignment:<br>
      A. Remove destination field<br>
      B. Use ID-based filtering with header and checksum<br>
      C. Increase OLED text size<br>
      D. Disable ACK<br>
    </li>
  <li>Receiver sends ACK, but TX still times out occasionally under interference. Best practical adjustment:<br>
      A. Add retry with backoff and tune ACK timeout<br>
      B. Remove checksum<br>
      C. Switch ACK to retained topic<br>
      D. Use Wi-Fi instead<br>
    </li>
  <li>OLED shows gibberish or nothing while radio works. Most likely display-side issue:<br>
      A. Wrong OLED dimensions/config (128x32 vs 128x64)<br>
      B. Wrong LoRa frequency<br>
      C. ACK timeout too long<br>
      D. Node ID mismatch<br>
    </li>
  <li>Receiver should send NACK when:<br>
      A. Destination is different node<br>
      B. Checksum validation fails on message for this node<br>
      C. RSSI is low<br>
      D. Payload contains text only<br>
    </li>
  <li>Two nodes share same nodeID accidentally. Likely effect:<br>
      A. Cleaner routing<br>
      B. Ambiguous addressing/ACK confusion<br>
      C. Better checksum reliability<br>
      D. No impact<br>
    </li>
  <li>Why call `waitPacketSent()` after send?<br>
      A. Ensures packet transmission completes before next logic<br>
      B. Generates checksum<br>
      C. Clears OLED buffer<br>
      D. Enables receive interrupt<br>
    </li>
</ol>

## Answers
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
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
</ol>


