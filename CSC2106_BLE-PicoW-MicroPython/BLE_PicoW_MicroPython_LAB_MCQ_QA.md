

> For a much larger code-focused question set, use [MEGA_CODE_UNDERSTANDING_QBANK.md](../MEGA_CODE_UNDERSTANDING_QBANK.md) or the root file in this repo.

## Lab Test Format Alignment
- Focus: no coding required.
- Question type: MCQ / multi-select style conceptual understanding.
- Count: 20 questions (practice set).

## Section A: Lab-Test Style Questions (20)

<ol>
  <li>In this lab, the board that advertises and hosts GATT data is the:<br>
      A. Observer<br>
      B. Broadcaster-only<br>
      C. Peripheral<br>
      D. Scanner<br>
    </li>
  <li>The board that scans and initiates connection is the:<br>
      A. Peripheral<br>
      B. Central<br>
      C. Beacon<br>
      D. GATT server only<br>
    </li>
  <li>The custom service UUID used is:<br>
      A. 0x1A00<br>
      B. 0x1A01<br>
      C. 0x1A02<br>
      D. 0x2902<br>
    </li>
  <li>The LED characteristic UUID is:<br>
      A. 0x1A00<br>
      B. 0x1A01<br>
      C. 0x1A02<br>
      D. 0x180A<br>
    </li>
  <li>The status characteristic supports:<br>
      A. WRITE only<br>
      B. READ and NOTIFY<br>
      C. NOTIFY only<br>
      D. INDICATE only<br>
    </li>
  <li>Why is CCCD needed?<br>
      A. To discover services<br>
      B. To encrypt packets<br>
      C. To configure notifications and indications<br>
      D. To set advertising interval<br>
    </li>
  <li>Value to enable notifications is:<br>
      A. 0x0001<br>
      B. 0x0100<br>
      C. 0x1000<br>
      D. 0xFFFF<br>
    </li>
  <li>A likely reason notify fails while read works is:<br>
      A. Client did not write CCCD<br>
      B. RSSI too strong<br>
      C. Service UUID too short<br>
      D. Pull-up resistor enabled<br>
    </li>
  <li>Button debounce is used to:<br>
      A. Increase BLE range<br>
      B. Prevent repeated false presses<br>
      C. Improve UUID matching<br>
      D. Force reconnect<br>
    </li>
  <li>Characteristic discovery should happen:<br>
      A. Before scan<br>
      B. After successful connect and service discovery<br>
      C. Only after disconnect<br>
      D. Only on server<br>
    </li>
  <li>Correct Task 4 path is:<br>
      A. Client write -> server write IRQ -> server LED update<br>
      B. Server notify -> client write<br>
      C. Client read -> server disconnect<br>
      D. CCCD write -> LED toggles<br>
    </li>
  <li>Notify is often better than polling because:<br>
      A. No stack needed<br>
      B. Server pushes updates only when needed<br>
      C. UUID is not required<br>
      D. It disables interrupts<br>
    </li>
  <li>On disconnect, server should:<br>
      A. Stop BLE forever<br>
      B. Re-advertise<br>
      C. Delete service<br>
      D. Reset board<br>
    </li>
  <li>BLE logic in this lab is mostly:<br>
      A. Event driven using IRQ callbacks<br>
      B. Blocking synchronous only<br>
      C. Interrupt disabled loops<br>
      D. File system driven<br>
    </li>
  <li>A practical recovery when discovery fails is:<br>
      A. Guess random handles<br>
      B. Disconnect and retry cleanly<br>
      C. Disable notifications permanently<br>
      D. Change ADC pin<br>
    </li>
  <li>Beacon mode differs because beacon:<br>
      A. Never advertises<br>
      B. Advertises only and does not run connectable GATT exchange<br>
      C. Requires Wi-Fi<br>
      D. Uses only write requests<br>
    </li>
  <li>CCCD UUID is:<br>
      A. 0x2A00<br>
      B. 0x2800<br>
      C. 0x2902<br>
      D. 0x180D<br>
    </li>
  <li>BLE handle usually refers to:<br>
      A. Pin number<br>
      B. ATT attribute index<br>
      C. Device name pointer<br>
      D. RSSI threshold<br>
    </li>
  <li>If target name filter mismatches, client likely:<br>
      A. Still connects to target<br>
      B. Fails to find intended server<br>
      C. Enables notify anyway<br>
      D. Skips scan stage<br>
    </li>
  <li>Why avoid hardcoded handles?<br>
      A. Handles are encrypted<br>
      B. Runtime layout can change handle values<br>
      C. Handles are Wi-Fi specific<br>
      D. Handles are random each packet<br>
    </li>
</ol>

## Answers
<ol>
  <li>C</li>
  <li>B</li>
  <li>C</li>
  <li>A</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
</ol>


