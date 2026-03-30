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

## Section C: Code-Trace MCQs + Short Answers

### C1. Code-Trace MCQs
<ol>
  <li>Packet keeps looping forever among nodes. Most likely missing control is:<br>
    A. Adv interval tuning only
    B. Dedupe and TTL decrement checks
    C. ADC conversion
    D. Node ID print
  </li>
  <li>Node re-forwards its own injected packet after hearing bounce. Best fix is:<br>
    A. Disable scan
    B. Add own ORIG:MSGID to seen cache during inject
    C. Increase scan window
    D. Remove forward function
  </li>
  <li>Assignment trigger fires from R packets. Correct filter rule is:<br>
    A. Count every type
    B. Exclude type R from counting
    C. Count only duplicates
    D. Count self-origin only
  </li>
  <li>After some time node stops hearing anything. Likely missing behavior:
    A. Restart scan in scan-done IRQ
    B. Recompute NODE_ID
    C. Reset seen cache each loop
    D. Force advertise always on
  </li>
  <li>Forwarded frame TTL not reducing across hops. Most likely bug:
    A. Using raw forward without rebuilding frame with ttl-1
    B. Using RSSI in print
    C. Short payload data
    D. Jitter enabled
  </li>
</ol>

### Answers: C1
<ol>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
</ol>

### C2. Short-Answer Bank (Model Answers)
1. Question: Why is ORIG plus MSGID enough for dedupe identity?
Model answer: ORIG identifies source node and MSGID identifies message instance from that source; pair uniquely represents a logical flooded packet.

2. Question: Why should R packets use TTL 0 in assignment extension?
Model answer: R is local response summary and should not propagate flood-wide; TTL 0 enforces non-forward behavior.

3. Question: Why inject trigger logic inside RX path instead of timer loop?
Model answer: Trigger condition depends on arrival of valid new packets; RX path has exact filtering context (dedupe/self/type).

4. Question: What happens if seen cache is too small?
Model answer: Old keys evict too quickly, repeated packets may be treated as new and re-forwarded, increasing unnecessary traffic.

5. Question: Why keep advertise as short burst instead of always-on transmit?
Model answer: Burst limits airtime occupancy and better models discrete packet forwarding/injection behavior.
