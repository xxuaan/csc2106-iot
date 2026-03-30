# Simple BLE Flooding Mesh MCQ Questions and Answers

## Section A: Core MCQs

<ol>
  <li>This lab's mesh behavior is implemented using:<br>
    A. Bluetooth SIG Mesh stack<br>
    B. BLE advertising and scanning with application-level flooding<br>
    C. Wi-Fi multicast only<br>
    D. Zigbee coordinator mode
  </li>
  <li>A frame identity key for dedupe is typically:<br>
    A. `(TYPE, TTL)`<br>
    B. `(ORIG, MSGID)`<br>
    C. `(RSSI, DATA)`<br>
    D. `(ADDR, ADV_TYPE)`
  </li>
  <li>TTL in flooding primarily limits:<br>
    A. Payload size<br>
    B. Number of forwarding hops<br>
    C. RSSI range<br>
    D. Scan interval
  </li>
  <li>In Part 2, forwarding all packets without controls can cause:<br>
    A. Better reliability only<br>
    B. Ping-pong storms<br>
    C. Zero traffic<br>
    D. Mandatory encryption
  </li>
  <li>A message with TTL 0 should generally be:<br>
    A. Forwarded with TTL -1<br>
    B. Not forwarded further<br>
    C. Converted to ACK<br>
    D. Deleted from parser
  </li>
  <li>Seen-cache purpose is to:<br>
    A. Increase sensor accuracy<br>
    B. Avoid processing same logical packet repeatedly<br>
    C. Set Wi-Fi SSID<br>
    D. Build OLED fonts
  </li>
  <li>Marking own injected message as seen prevents:<br>
    A. Initial transmission<br>
    B. Re-forwarding own packet when heard again<br>
    C. Temperature conversion<br>
    D. Scan restarts
  </li>
  <li>Assignment trigger count should ignore which type?<br>
    A. `T`<br>
    B. `R`<br>
    C. Any non-self message<br>
    D. All messages
  </li>
  <li>Assignment response message should use:<br>
    A. TYPE=`R`, TTL=0<br>
    B. TYPE=`T`, TTL=3<br>
    C. TYPE=`R`, TTL=3<br>
    D. TYPE=`A`, TTL=1
  </li>
  <li>After scan window ends, robust behavior is to:<br>
    A. Stop permanently<br>
    B. Restart scanning in scan-done event<br>
    C. Reboot board<br>
    D. Disconnect BLE
  </li>
  <li>Advertise burst service function mainly ensures:<br>
    A. Advertising stops after burst duration<br>
    B. Scan buffer clears<br>
    C. TTL increments<br>
    D. Random ID generation
  </li>
  <li>Event-driven injection in assignment means injection is triggered by:<br>
    A. Fixed periodic timer only<br>
    B. Receiving 5 valid new messages<br>
    C. Every scan done<br>
    D. Any duplicate packet
  </li>
</ol>

### Answers: Section A
<ol>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="13">
  <li>Node still forwards packets indefinitely after Part 3 update. Most likely missed change:<br>
    A. TTL decrement not applied before forward decision<br>
    B. Scan interval too short<br>
    C. ADC not initialized<br>
    D. NODE_ID too long
  </li>
  <li>Node keeps forwarding its own packet after hearing it back from neighbor. Best fix:<br>
    A. Increase ADV interval only<br>
    B. Add own `(ORIG, MSGID)` to seen cache at injection time<br>
    C. Disable scan<br>
    D. Force TTL to 10
  </li>
  <li>`R` packets are causing extra trigger counts. Correct logic should:<br>
    A. Count all types equally<br>
    B. Exclude type `R` from counting and forwarding<br>
    C. Convert `R` to `T`<br>
    D. Drop all non-self packets
  </li>
  <li>Same `(ORIG, MSGID)` appears many times with different RSSI. Correct handling is:<br>
    A. Treat each RSSI as new packet<br>
    B. Process once and drop subsequent duplicates<br>
    C. Always forward strongest only<br>
    D. Reset seen cache every message
  </li>
  <li>Seen cache too small in dense traffic may lead to:<br>
    A. Old entries evicted too quickly, duplicates reappear as new<br>
    B. BLE hardware reset<br>
    C. Invalid frame format<br>
    D. TTL never decrements
  </li>
  <li>If scan is not restarted on scan-done, expected symptom is:<br>
    A. Node continues receiving forever<br>
    B. Node eventually stops receiving packets<br>
    C. Node injects twice as fast<br>
    D. Dedupe fails immediately
  </li>
  <li>Forwarding raw frame without changing TTL breaks which control principle?<br>
    A. Parser compatibility<br>
    B. Bounded propagation<br>
    C. Node ID uniqueness<br>
    D. Sensor sampling
  </li>
  <li>Best reason to keep frame short in adv name field:<br>
    A. BLE adv payload has limited practical capacity<br>
    B. JSON requires short strings<br>
    C. TTL only supports short packets<br>
    D. Scan callback truncates by design always
  </li>
</ol>

### Answers: Section B
<ol start="13">
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
</ol>
