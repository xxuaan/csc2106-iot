# BLE Pico W Lab MCQ Questions and Answers

## Section A: Core MCQs

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
  <li>Read flow requires:<br>
    A. gattc_read plus read result/done events<br>
    B. CCCD write only<br>
    C. gap_advertise call<br>
    D. Service registration on client<br>
  </li>
  <li>WRITE_NO_RESPONSE is useful when:<br>
    A. Lowest overhead and low latency are preferred<br>
    B. Maximum acknowledgment is required<br>
    C. Read is disabled<br>
    D. Scan is disabled<br>
  </li>
  <li>Server notify targets:<br>
    A. All relevant active connection handles<br>
    B. Only first client forever<br>
    C. No clients unless scanning<br>
    D. Only disconnected clients<br>
  </li>
  <li>Robust write handling should:<br>
    A. Crash on parse errors<br>
    B. Validate and safely handle malformed values<br>
    C. Ignore all writes<br>
    D. Disable LED updates<br>
  </li>
  <li>LED state value format in this lab is generally:<br>
    A. float32 binary<br>
    B. UTF-8 bytes for 0 or 1<br>
    C. encrypted JSON only<br>
    D. UUID text<br>
  </li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="26">
  <li>Client logs show service found and characteristics found, but no notify events appear. Most probable direct cause is:<br>
    A. Wrong advertising interval<br>
    B. CCCD was not written successfully<br>
    C. LED characteristic is read only<br>
    D. Client scan window too small<br>
  </li>
  <li>Client reads LED correctly but writes do nothing on server. Most likely issue is:<br>
    A. Wrong LED characteristic handle used for write path<br>
    B. Status string decode failed<br>
    C. Client button debounce too high<br>
    D. ADC formula is incorrect<br>
  </li>
  <li>Server disconnect event happens and client cannot reconnect unless server is reset. Most likely missing logic:<br>
    A. Re-register services<br>
    B. Re-enable ADC<br>
    C. Re-start advertising on disconnect<br>
    D. Rebuild UUID objects<br>
  </li>
  <li>Client found target name, connected, then disconnected after service discovery done with no target service found. Most likely explanation:<br>
    A. Service UUID mismatch between server and client<br>
    B. RSSI too low for read<br>
    C. Status characteristic not notifiable<br>
    D. LED write mode set to no response<br>
  </li>
  <li>Descriptor discovery returns no CCCD, and client uses status_handle plus 1 fallback. Primary risk is:<br>
    A. Battery drain only<br>
    B. Writing to wrong attribute if layout assumptions are false<br>
    C. Device name corruption<br>
    D. Duplicate advertising packets<br>
  </li>
  <li>User presses Button B once but server toggles LED multiple times. Most likely immediate fix:<br>
    A. Decrease MTU size<br>
    B. Add or improve button debounce logic<br>
    C. Disable notifications<br>
    D. Increase advertising payload size<br>
  </li>
  <li>Server crashes occasionally on incoming writes from unknown clients. Most robust coding improvement:<br>
    A. Add try/except and range validation on parsed LED value<br>
    B. Disable write support<br>
    C. Remove IRQ handler<br>
    D. Only allow reads<br>
  </li>
  <li>In a multi-client future extension, server stores one conn_handle only. Likely issue:<br>
    A. Only one client may receive notifications correctly<br>
    B. UUID conflict at boot<br>
    C. Service registration fails immediately<br>
    D. Client scan cannot stop<br>
  </li>
  <li>Client receives notify on unexpected handle and ignores data. Correct interpretation:<br>
    A. Bug in BLE hardware<br>
    B. Handler should verify the handle map from discovery and match correctly<br>
    C. Advertising packet malformed<br>
    D. ADC channel unavailable<br>
  </li>
  <li>Client starts scanning indefinitely and never connects even though server is present. Most diagnostic first step:<br>
    A. Verify target name in scan filter matches actual advertised name exactly<br>
    B. Change button pin numbers<br>
    C. Disable status characteristic<br>
    D. Force CCCD write before connect<br>
  </li>
  <li>Why might WRITE_NO_RESPONSE be selected for LED control in this lab?<br>
    A. It can reduce transaction overhead for simple state updates<br>
    B. It guarantees stronger delivery semantics than write with response<br>
    C. It enables notifications automatically<br>
    D. It bypasses service discovery<br>
  </li>
  <li>Client local LED toggles immediately on button write, but server LED remains unchanged due to failed write. Best UI improvement:<br>
    A. Update local LED only after write success callback<br>
    B. Remove local LED usage entirely<br>
    C. Toggle local LED twice<br>
    D. Increase sleep delay<br>
  </li>
  <li>Notify path works once, then not again after reconnect. Most likely design oversight:<br>
    A. CCCD subscription was not re-established on new connection<br>
    B. Service UUID became invalid<br>
    C. ADC conversion changed MTU<br>
    D. Scan interval too short<br>
  </li>
  <li>In event-driven BLE code, race conditions are reduced by:<br>
    A. Assuming event order never changes<br>
    B. Tracking connection and handle state explicitly and checking before actions<br>
    C. Removing all callbacks<br>
    D. Forcing fixed delays only<br>
  </li>
  <li>Which statement best captures read result versus read done semantics?<br>
    A. Both carry identical payload always<br>
    B. Read result carries value data; read done indicates operation completion status<br>
    C. Read done always arrives first<br>
    D. Read result is only for notify<br>
  </li>
</ol>

## Section C: Code-Trace MCQs + Short Answers

### C1. Code-Trace MCQs
<ol>
  <li>Client connects but never receives NOTIFY. Which code step is most likely missing?<br>
    A. gattc_read call<br>
    B. CCCD write to enable notifications<br>
    C. gap_disconnect call<br>
    D. LED write call<br>
  </li>
  <li>Server receives malformed LED payload and may crash. Best hardening point is:<br>
    A. In scan callback<br>
    B. In server write IRQ parse block with validation/try-catch<br>
    C. In advertise function only<br>
    D. In temperature conversion<br>
  </li>
  <li>Client can read LED but cannot write LED. Most likely code mismatch is:<br>
    A. Wrong status handle used for write path<br>
    B. Wrong ADC channel<br>
    C. Wrong scan interval only<br>
    D. Wrong debounce interval only<br>
  </li>
  <li>After disconnect, clients cannot reconnect to server. Which server branch is key?<br>
    A. GATTS write branch<br>
    B. Disconnect IRQ branch that restarts advertising<br>
    C. Notify branch only<br>
    D. Temperature read function<br>
  </li>
  <li>Notify callback fires but data ignored. Most likely code-level reason:<br>
    A. Missing RSSI print<br>
    B. Value handle does not match stored status handle<br>
    C. LED pin mode is output<br>
    D. Name filter too long<br>
  </li>
</ol>

### C2. Short-Answer Bank

1. Explain full client startup event sequence from scan to notifications.
2. Why keep connection handles in a set on server?
3. Why discovered handles are mandatory instead of hardcoded values?
4. Distinguish READ_RESULT vs READ_DONE.
5. Why use debounce around button actions in client loop?

## Answers (All Sections)

### Section A
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
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section B
<ol start="26">
  <li>B</li>
  <li>A</li>
  <li>C</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section C
<ol>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section C2
1. Scan finds target name, scan stops, connect starts, service and characteristic discovery run, descriptor discovery finds CCCD, client writes 0x0100 to CCCD, then notify events are delivered on status handle.
2. Multiple centrals can connect over time; set allows tracking active links and notifying each connection safely.
3. Handle layout can differ by registration order and runtime; discovery ensures read/write/notify operations target correct attributes.
4. READ_RESULT carries returned value payload; READ_DONE gives completion status code for transaction success/failure.
5. Mechanical bounce can trigger multiple rapid writes/reads from one press; debounce enforces a minimum action interval.

