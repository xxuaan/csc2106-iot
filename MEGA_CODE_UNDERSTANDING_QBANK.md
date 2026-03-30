# CSC2106 IoT Mega Code-Understanding Question Bank

Use this for lab-test prep (no coding required).
Goal: understand how every lab code path works (regular + assignment files).
More snippet-only practice (20 per topic): see SNIPPET_STYLE_QBANK_20_PER_TOPIC.md

## BLE (Pico W) - Code Understanding (20)
Code coverage:
- assignment/ble_server.py
- assignment/ble_client.py

1. Why does the client discover service and characteristic handles after connect instead of hardcoding them?<br>
A. Handles are encrypted<br>
B. Handles can change by runtime registration order<br>
C. Hardcoded handles are faster but forbidden<br>
D. Because UUIDs are optional<br>

2. In BLE server IRQ handling, which event is used to process a client write to LED characteristic?<br>
A. _IRQ_GATTC_NOTIFY<br>
B. _IRQ_CENTRAL_CONNECT<br>
C. _IRQ_GATTS_WRITE<br>
D. _IRQ_SCAN_RESULT<br>

3. Why is CCCD write required for notifications?<br>
A. It enables advertising<br>
B. It enables per-connection notify subscription<br>
C. It resets MTU<br>
D. It replaces characteristic discovery<br>

4. Client receives read value through which path?<br>
A. write done event only<br>
B. immediate return from gattc_read()<br>
C. read result IRQ event<br>
D. scan done event<br>

5. If notify is not received but read works, most likely cause is:<br>
A. LED pin mismatch<br>
B. CCCD not written with 0x0100<br>
C. Wi-Fi disconnected<br>
D. wrong button pull-up<br>

6. Why should server re-advertise on disconnect?<br>
A. To clear flash<br>
B. To remain discoverable for reconnect<br>
C. To reset UUIDs<br>
D. To force beacon mode<br>

7. In this lab, status characteristic permissions should include:<br>
A. WRITE only<br>
B. READ and NOTIFY<br>
C. NOTIFY only, no READ<br>
D. INDICATE only<br>

8. What is the role of debounce around client buttons?<br>
A. Increase BLE range<br>
B. Prevent repeated rapid read/write triggers<br>
C. Improve RSSI<br>
D. Enable notifications<br>

9. Why is event-driven logic mandatory for BLE in MicroPython?<br>
A. BLE is synchronous only<br>
B. Operations complete asynchronously and signal via IRQ<br>
C. IRQ is optional and discouraged<br>
D. Polling is always better<br>

10. Which descriptor UUID is used for notification config?<br>
A. 0x180A<br>
B. 0x1A02<br>
C. 0x2902<br>
D. 0x2A00<br>

11. Multi-select: robust server write handling should include:<br>
A. decode bytes safely<br>
B. validate allowed LED values<br>
C. parse with exception protection<br>
D. reboot on any parse error<br>

12. Why does client stop scan before connect attempt?<br>
A. Required to avoid conflicting state transitions<br>
B. To disable notifications<br>
C. To increase battery temperature<br>
D. Because service discovery needs scan active<br>

13. Which value enables notifications in CCCD write payload?<br>
A. b'\x00\x01'<br>
B. b'\x01\x00'<br>
C. b'\x10\x00'<br>
D. b'\xff\xff'<br>

14. A correct write path in this lab is:<br>
A. Client button -> gattc_write -> server _IRQ_GATTS_WRITE -> LED update<br>
B. Client button -> notify -> server read<br>
C. Client button -> scan -> LED update<br>
D. Client button -> descriptor read -> LED update<br>

15. Why keep connected handles in a collection on server side?<br>
A. Track active centrals for notifications<br>
B. Store Wi-Fi clients<br>
C. Cache ADC samples<br>
D. Replace characteristic handles<br>

16. Multi-select: if discovery fails, good recovery steps are:<br>
A. disconnect and retry cleanly<br>
B. verify UUID constants match<br>
C. guess random handles<br>
D. log event sequence checkpoints<br>

17. Why does beacon mode not support read/write/notify in this lab context?<br>
A. It is advertising-only without GATT connection lifecycle<br>
B. Beacon has no UUID<br>
C. Beacon disables BLE radio<br>
D. Beacon requires MQTT<br>

18. Which side initiates connection in this lab?<br>
A. Peripheral/server<br>
B. Central/client<br>
C. Both simultaneously<br>
D. Neither, it is broadcast-only<br>

19. LED state payload format is most commonly:<br>
A. float32 binary blob<br>
B. utf-8 text bytes for 0/1<br>
C. encrypted JSON only<br>
D. UUID text<br>

20. Multi-select: strongest signs that BLE flow is healthy are:<br>
A. scan finds target name<br>
B. service/char/descriptor discovery succeeds<br>
C. CCCD write success<br>
D. notify callback receives expected handle/value<br>

### BLE Answers
1. B
2. C
3. B
4. C
5. B
6. B
7. B
8. B
9. B
10. C
11. A,B,C
12. A
13. B
14. A
15. A
16. A,B,D
17. A
18. B
19. B
20. A,B,C,D

## REST (Pico W) - Code Understanding (20)
Code coverage:
- assignment/main.py
- assignment/web_server.py

1. Why split code into main.py and web_server.py?<br>
A. main handles HTTP only<br>
B. clear separation: hardware loop vs HTTP parsing/routing<br>
C. avoid socket usage<br>
D. disable error handling<br>

2. In this lab, final LED hardware write is done by:<br>
A. browser<br>
B. web_server parser directly only<br>
C. main loop after returned command value<br>
D. DNS resolver<br>

3. Endpoint + method pair for LED control is:<br>
A. GET /led<br>
B. POST /led<br>
C. DELETE /led<br>
D. PUT /temp<br>

4. If route exists but wrong method is used, return:<br>
A. 404<br>
B. 400<br>
C. 405<br>
D. 500<br>

5. Why set Content-Type application/json for /status?<br>
A. required for machine-readable contract<br>
B. increase Wi-Fi speed<br>
C. avoid sockets<br>
D. enable BLE<br>

6. Valid JSON body to turn LED on:<br>
A. {"state":1}<br>
B. {"state":"on"}<br>
C. {"led":true,"x":9}<br>
D. {"value":"high"}<br>

7. Why validate LED state to 0/1?<br>
A. deterministic actuator behavior and input safety<br>
B. reduce ADC noise only<br>
C. increase TCP window<br>
D. avoid returning status code<br>

8. What does bind address 0.0.0.0 mean?<br>
A. localhost only<br>
B. all interfaces<br>
C. disable network<br>
D. gateway route only<br>

9. Unknown path should return:<br>
A. 200 root<br>
B. 302<br>
C. 404<br>
D. 405<br>

10. Multi-select: robust POST /led parser should support:<br>
A. application/json<br>
B. application/x-www-form-urlencoded<br>
C. plain text 0/1<br>
D. random binary without validation<br>

11. Why pass current_led_value into serve_client?<br>
A. let /status reflect actual runtime actuator state<br>
B. speed reconnect<br>
C. recalc temperature<br>
D. avoid parsing request<br>

12. In this lab, temperature comes from:<br>
A. external DHT22 only<br>
B. ADC(4) internal sensor formula<br>
C. HTTP headers<br>
D. broker status topic<br>

13. A malformed /led request should typically return:<br>
A. 200<br>
B. 201<br>
C. 400<br>
D. 405<br>

14. Why not return 500 for bad client payload?<br>
A. 500 means server fault, not client format fault<br>
B. 500 is deprecated<br>
C. 500 is faster<br>
D. 500 required for POST<br>

15. Multi-select: good route behavior matrix is:<br>
A. GET /temp -> 200<br>
B. POST /led valid -> 200 + command<br>
C. wrong method on known route -> 405<br>
D. unknown route -> 404<br>

16. Risk of simplistic recv(1024) parsing:<br>
A. fragmented/large requests may be incomplete<br>
B. LED pin inversion<br>
C. ADC overflow only<br>
D. TLS certificate loss<br>

17. Why keep hardware actuation out of deep parser branches?<br>
A. improves separation of concerns and predictable flow<br>
B. required by HTTP spec<br>
C. disables exceptions<br>
D. allows no status codes<br>

18. Which endpoint is expected to return JSON status object?<br>
A. /<br>
B. /temp<br>
C. /status<br>
D. /hello<br>

19. Multi-select: correct /status fields for assignment style are:<br>
A. temperature<br>
B. led_on<br>
C. random_seed<br>
D. uptime_millis mandatory<br>

20. Main loop server pattern is:<br>
A. accept -> parse -> apply LED command if needed -> continue<br>
B. parse before accept<br>
C. publish MQTT before every response<br>
D. restart Wi-Fi every request<br>

### REST Answers
1. B
2. C
3. B
4. C
5. A
6. A
7. A
8. B
9. C
10. A,B,C
11. A
12. B
13. C
14. A
15. A,B,C,D
16. A
17. A
18. C
19. A,B
20. A

## MQTT (Pico W) - Code Understanding (20)
Code coverage:
- picoA.py, picoB.py, simple.py
- assignment/nodeA.py, assignment/nodeB.py, assignment/simple.py

1. MQTT communication model in this lab is:<br>
A. direct peer socket only<br>
B. publish-subscribe via broker<br>
C. BLE notify mesh<br>
D. HTTP request-response<br>

2. Why use retained status topics?<br>
A. late subscribers get latest state immediately<br>
B. to replay command events<br>
C. to reduce keepalive to zero<br>
D. to disable callback<br>

3. Why avoid retain on command topics?<br>
A. stale command replay can trigger unwanted actions<br>
B. broker forbids retain on commands<br>
C. QoS1 disables retain<br>
D. command topics cannot be subscribed<br>

4. In umqtt.simple, callback execution depends on:<br>
A. set_callback only<br>
B. check_msg loop polling<br>
C. publish loop only<br>
D. reconnect only<br>

5. LWT is published when:<br>
A. graceful disconnect always<br>
B. unexpected client disconnect<br>
C. any subscribe<br>
D. any retained publish<br>

6. Multi-select: reconnect-safe flow should include:<br>
A. recreate client<br>
B. set callback<br>
C. re-subscribe topics<br>
D. re-publish online status if needed<br>

7. QoS1 means:<br>
A. exactly once<br>
B. at least once (duplicates possible)<br>
C. at most once<br>
D. no broker ack path<br>

8. Duplicate effects are risky for TOGGLE because:<br>
A. toggle is non-idempotent<br>
B. QoS1 forbids duplicate<br>
C. button pin locked<br>
D. retain auto-fixes duplicates<br>

9. In bidirectional assignment, each node should be:<br>
A. publisher only<br>
B. subscriber only<br>
C. both publisher and subscriber<br>
D. broker itself<br>

10. Using same client ID on two devices often causes:<br>
A. stable coexistence guaranteed<br>
B. broker disconnects one session<br>
C. merged topics permanently<br>
D. no effect<br>

11. Multi-select: common reasons callback never fires are:<br>
A. check_msg not called<br>
B. wrong subscribed topic string<br>
C. callback not set<br>
D. Wi-Fi and broker unreachable<br>

12. Why set_last_will before connect?<br>
A. will must be registered at session creation<br>
B. faster LED toggle<br>
C. required for GPIO pull-up<br>
D. replaces subscribe<br>

13. Typical broker port used here:<br>
A. 80<br>
B. 443<br>
C. 1883<br>
D. 3306<br>

14. Edge detection on button is used to:<br>
A. avoid repeated publishes while held<br>
B. increase QoS<br>
C. avoid subscribe calls<br>
D. decode JSON<br>

15. Multi-select: message design separation should keep:<br>
A. command topics for events<br>
B. status topics for retained state<br>
C. ack topics usually non-retained<br>
D. everything in one topic string<br>

16. In nodeA/nodeB assignment, correct concept is:<br>
A. A publishes to B command topic; B publishes to A command topic<br>
B. both publish to exact same loop topic only<br>
C. no subscriptions needed<br>
D. broker writes GPIO directly<br>

17. Why wrap publish/check in try/except?<br>
A. recover from transient network/broker failures<br>
B. increase payload size<br>
C. bypass keepalive<br>
D. disable callback<br>

18. Multi-select: startup order should usually be:<br>
A. connect wifi<br>
B. create/connect mqtt client<br>
C. set callback + subscribe<br>
D. enter check_msg/button loop<br>

19. Last will payload in these labs typically indicates:<br>
A. online<br>
B. offline<br>
C. toggle<br>
D. ack<br>

20. Why exact topic matching matters?<br>
A. one path segment mismatch means intended subscriber may never receive<br>
B. broker auto-corrects typos<br>
C. wildcard applies automatically without subscription<br>
D. QoS1 fixes wrong topic names<br>

### MQTT Answers
1. B
2. A
3. A
4. B
5. B
6. A,B,C,D
7. B
8. A
9. C
10. B
11. A,B,C,D
12. A
13. C
14. A
15. A,B,C
16. A
17. A
18. A,B,C,D
19. B
20. A

## Simple BLE Flooding Mesh - Code Understanding (20)
Code coverage:
- main.py
- assignment/mesh_node.py

1. This lab's mesh is:<br>
A. official Bluetooth SIG Mesh<br>
B. application-level flooding over BLE advertise/scan<br>
C. Wi-Fi mesh only<br>
D. LoRa mesh<br>

2. Frame identity for dedupe is:<br>
A. TYPE+TTL<br>
B. ORIG+MSGID<br>
C. RSSI+DATA<br>
D. ADV_ADDR only<br>

3. Why decrement TTL on forward?<br>
A. bound propagation and prevent endless rebroadcast<br>
B. improve ADC precision<br>
C. increase payload length<br>
D. replace dedupe cache<br>

4. If dedupe is disabled, likely outcome is:<br>
A. packet storms/ping-pong loops<br>
B. no transmissions<br>
C. automatic encryption<br>
D. better battery life<br>

5. ADV burst stop service function exists to:<br>
A. stop advertising after configured burst duration<br>
B. reset NODE_ID<br>
C. clear Wi-Fi cache<br>
D. set MQTT retain<br>

6. Why restart scan on scan-done event?<br>
A. keep reception continuous over time<br>
B. force disconnection<br>
C. reduce parsing logic<br>
D. increment TTL<br>

7. In assignment trigger logic, count excludes:<br>
A. self-origin packets<br>
B. R-type packets<br>
C. duplicates<br>
D. all of the above<br>

8. Why mark own injected message as seen immediately?<br>
A. avoid re-forwarding own message when heard back<br>
B. increase RSSI<br>
C. disable scan<br>
D. bypass parser<br>

9. Multi-select: valid forward decision usually requires:<br>
A. packet parsed successfully<br>
B. packet is new (not seen)<br>
C. type not blocked by policy<br>
D. ttl > 0<br>

10. Frame format prefix used in code is typically:<br>
A. BLE1|<br>
B. M1|<br>
C. MSG|<br>
D. ADV|<br>

11. Why keep payload short in adv name path?<br>
A. advertising payload capacity is limited<br>
B. UUID must be 2 bytes only<br>
C. ttl field must be omitted<br>
D. parser only accepts 3 chars<br>

12. Injection schedule uses jitter mainly to:<br>
A. reduce synchronized collisions across nodes<br>
B. increase fixed periodicity<br>
C. disable forwarding<br>
D. force node reboot<br>

13. Multi-select: seen cache tradeoff with very small size is:<br>
A. old entries evict quickly<br>
B. duplicates may reappear as new<br>
C. unnecessary forwarding can rise<br>
D. no effect on behavior<br>

14. In assignment extension, R message TTL is set to 0 to:<br>
A. keep it local/non-forwarded<br>
B. maximize hops<br>
C. replace T-type traffic<br>
D. disable scan<br>

15. If parser accepts malformed frame lengths, risk is:<br>
A. incorrect field extraction and unstable logic<br>
B. stronger dedupe<br>
C. lower CPU use<br>
D. guaranteed reliability<br>

16. Multi-select: robust mesh debug logs should show:<br>
A. RX NEW vs RX DUP<br>
B. forward decisions and decremented TTL<br>
C. trigger count progress<br>
D. scan restart and adv stop events<br>

17. Why is this not full BLE Mesh standard?<br>
A. no SIG mesh provisioning/security/model stack here<br>
B. no radio usage<br>
C. no BLE advertisement<br>
D. cannot relay packets<br>

18. Core purpose of ORIG field is:<br>
A. identify original source across hops<br>
B. store frequency<br>
C. store rssi<br>
D. store local pin<br>

19. If TTL goes negative due to bug, expected fix is:<br>
A. clamp/drop when ttl2 < 0<br>
B. forward anyway<br>
C. convert to R type<br>
D. reset msgid<br>

20. Multi-select: event-driven trigger is better than timer for assignment because:<br>
A. tied directly to actual received traffic<br>
B. naturally uses dedupe/self/type filters in RX path<br>
C. avoids counting unrelated intervals<br>
D. guarantees zero packet loss<br>

### Mesh Answers
1. B
2. B
3. A
4. A
5. A
6. A
7. D
8. A
9. A,B,C,D
10. B
11. A
12. A
13. A,B,C
14. A
15. A
16. A,B,C,D
17. A
18. A
19. A
20. A,B,C

## LoRa - Code Understanding (20)
Code coverage:
- rf95_tx.ino, rf95_rx.ino
- assignment/tx.ino, assignment/rx.ino
- ssd1306_i2c.ino, ssd1306_ascii.ino

1. In basic templates, transmission waits for completion using:<br>
A. rf95.available()<br>
B. rf95.waitPacketSent()<br>
C. rf95.lastRssi()<br>
D. rf95.setFrequency()<br>

2. TX/RX must match frequency because:<br>
A. different frequencies improve reliability<br>
B. channel mismatch prevents communication<br>
C. checksum adapts automatically<br>
D. ACK does not need frequency match<br>

3. Enhanced assignment packet includes:<br>
A. nodeID, destID, msgType, payloadLen, payload, checksum<br>
B. only payload text<br>
C. UUID and CCCD<br>
D. HTTP headers<br>

4. Checksum purpose in assignment is mainly:<br>
A. encryption<br>
B. corruption detection<br>
C. compression<br>
D. routing<br>

5. If ACK not received before timeout, TX should:<br>
A. assume success<br>
B. retry up to configured limit<br>
C. stop radio permanently<br>
D. change payload to empty<br>

6. Why use destID filtering on RX?<br>
A. ignore unrelated packets in multi-node channel<br>
B. increase OLED brightness<br>
C. set tx power<br>
D. remove checksum<br>

7. Multi-select: ACK validation on TX should verify:<br>
A. message type is ACK<br>
B. source/destination fields match expected relation<br>
C. checksum and parse validity<br>
D. any packet with strong RSSI is enough<br>

8. If RX prints data but TX still retries, common root cause is:<br>
A. ACK path mismatch or malformed ACK criteria<br>
B. tx power too high<br>
C. OLED font size<br>
D. serial baud only<br>

9. Why call rf95.waitAvailableTimeout(ms)?<br>
A. bounded wait for reply instead of blocking forever<br>
B. to send packet<br>
C. to compute checksum<br>
D. to clear OLED<br>

10. In assignment, NACK is typically sent when:<br>
A. checksum validation fails<br>
B. RSSI low<br>
C. payload text short<br>
D. nodeID equals destination<br>

11. Multi-select: parser safety checks should include:<br>
A. minimum length guard<br>
B. payloadLen upper bound<br>
C. remaining bytes sufficiency check<br>
D. skip checksum comparison<br>

12. OLED integration is useful primarily for:<br>
A. local runtime state visibility without serial monitor<br>
B. extending RF range<br>
C. changing frequency automatically<br>
D. replacing ACK protocol<br>

13. Template frequency often shown and assignment frequency can differ; implication is:<br>
A. both sides in same test must use same configured frequency<br>
B. mismatch is harmless<br>
C. ACK adapts to mismatch<br>
D. only TX frequency matters<br>

14. Multi-select: message type constants commonly represent:<br>
A. DATA<br>
B. ACK<br>
C. NACK<br>
D. ADC<br>

15. Why is waitPacketSent important before ACK wait loop?<br>
A. ensure TX finished before expecting response timing<br>
B. increases checksum quality<br>
C. clears display<br>
D. changes SPI mode<br>

16. If two nodes share same nodeID, likely issue is:<br>
A. ambiguous addressing and ACK confusion<br>
B. stronger dedupe<br>
C. no impact<br>
D. automatic conflict resolution in library<br>

17. Multi-select: robust retry logic should include:<br>
A. max retry cap<br>
B. timeout per attempt<br>
C. state/status logging<br>
D. infinite tight loop by default<br>

18. What does lastRssi help with?<br>
A. observing link quality<br>
B. parsing JSON<br>
C. generating checksum<br>
D. setting destination ID<br>

19. If RX must support broadcast, destID logic should accept:<br>
A. only own node id<br>
B. own node id and broadcast id value<br>
C. random ids<br>
D. ACK type only<br>

20. Multi-select: end-to-end healthy assignment run usually shows:<br>
A. TX send + wait logs<br>
B. RX parse + checksum pass logs<br>
C. RX ACK sent<br>
D. TX ACK received before retry limit<br>

### LoRa Answers
1. B
2. B
3. A
4. B
5. B
6. A
7. A,B,C
8. A
9. A
10. A
11. A,B,C
12. A
13. A
14. A,B,C
15. A
16. A
17. A,B,C
18. A
19. B
20. A,B,C,D

## How To Use This Bank
1. Do one topic at a time without looking at answers.
2. For every wrong answer, open the exact code file and trace startup path + runtime path.
3. Re-attempt only wrong questions after 24 hours.
4. Before test day, do one mixed set of 20 from all topics.

## Snippet-Style Mixed Mock (25)

1. Consider this BLE client snippet:<br>
```python
self._ble.gattc_write(conn_handle, self._cccd_handle, b'\x01\x00', 0)
```
What is the primary effect?<br>
A. Enables notifications for that characteristic on this connection<br>
B. Writes LED OFF state<br>
C. Starts advertising<br>
D. Disconnects peripheral<br>

2. BLE server snippet:<br>
```python
elif event == _IRQ_GATTS_WRITE:
	conn_handle, value_handle = data
```
This branch runs when:<br>
A. Scan is done<br>
B. Client writes to a server characteristic<br>
C. Notify is received by client<br>
D. Service discovery starts<br>

3. BLE client snippet:<br>
```python
if addr_type == self._target_addr_type and bytes(addr) == self._target_addr:
```
Main purpose of this condition?<br>
A. Validate RSSI threshold<br>
B. Connect only to intended peripheral<br>
C. Decode notify payload<br>
D. Enable CCCD automatically<br>

4. BLE snippet:<br>
```python
self._ble.gap_scan(None)
```
Most likely meaning in context:<br>
A. Stop ongoing scan<br>
B. Start infinite scan<br>
C. Start advertising<br>
D. Reset BLE stack<br>

5. Multi-select. BLE read flow snippet:<br>
```python
self._ble.gattc_read(self._conn_handle, self._led_handle)
```
Correct statements:<br>
A. This requests read asynchronously<br>
B. Value is expected later via read-result IRQ event<br>
C. Returned value is immediately in function return<br>
D. Connection and handle must be valid first<br>

6. REST snippet:<br>
```python
s.bind(('0.0.0.0', 80))
```
Best interpretation:<br>
A. Listen on all interfaces, port 80<br>
B. Listen localhost only, port 80<br>
C. Connect to default gateway, port 80<br>
D. Open MQTT socket<br>

7. REST parser snippet:<br>
```python
if method != 'POST':
	return http_response(..., status=405)
```
Why 405 here?<br>
A. Unknown route<br>
B. Known route, wrong method<br>
C. Malformed body<br>
D. Server crash<br>

8. REST snippet:<br>
```python
body = json.dumps({'temperature': temp_c, 'led_on': bool(led_state)})
```
Reason to use json.dumps?<br>
A. Convert Python object to JSON string for response body<br>
B. Validate Wi-Fi credentials<br>
C. Parse URL parameters<br>
D. Encrypt response<br>

9. REST snippet:<br>
```python
new_state = serve_client(client, temp_c, led.value())
if new_state in (0, 1):
	led.value(new_state)
```
Design benefit:<br>
A. Keeps hardware actuation centralized in main loop<br>
B. Removes need for status codes<br>
C. Disables parsing errors<br>
D. Forces GET-only API<br>

10. Multi-select. REST LED parse snippet receives body `{"state":"1"}`.<br>
Good robust behavior is:<br>
A. Accept only if parser intentionally supports string-to-int conversion<br>
B. Reject with 400 if parser policy forbids this format<br>
C. Always return 500<br>
D. Ensure only final values 0/1 are applied<br>

11. MQTT snippet:<br>
```python
client.set_callback(on_msg)
client.subscribe(CMD_TOPIC)
```
What still must happen in loop for callback to run in umqtt.simple?<br>
A. client.check_msg()<br>
B. client.ping() only<br>
C. client.connect() every cycle<br>
D. client.subscribe() every cycle<br>

12. MQTT snippet:<br>
```python
client.set_last_will(STATUS_TOPIC, b'offline', retain=True, qos=1)
```
Meaning:<br>
A. Broker publishes offline status if client disconnects unexpectedly<br>
B. Client publishes offline every second<br>
C. Enables BLE advertising<br>
D. Forces QoS2 for all messages<br>

13. MQTT snippet:<br>
```python
if topic == CMD_TOPIC and msg == b'TOGGLE':
	led.value(0 if led.value() else 1)
```
Potential QoS1 caveat:<br>
A. Duplicate deliveries may toggle twice unintentionally<br>
B. QoS1 guarantees exactly once<br>
C. Topic matching is optional<br>
D. Callback runs only on retained messages<br>

14. MQTT snippet:<br>
```python
except Exception:
	client = make_client()
```
Why this pattern?<br>
A. Reconnect/recover after transient broker or network failure<br>
B. Improve GPIO speed<br>
C. Disable keepalive<br>
D. Remove callback need<br>

15. Multi-select. MQTT topic design best practice in this lab:<br>
A. Retained for status topics<br>
B. Non-retained for command/ack events<br>
C. Separate command and status topics<br>
D. Use one shared topic for everything to simplify parsing<br>

16. Mesh snippet:<br>
```python
key = '{}:{}'.format(orig, msgid)
if key in self.seen:
	return True
```
This mainly implements:<br>
A. De-duplication of logical packets<br>
B. Encryption key generation<br>
C. RSSI filtering<br>
D. Wi-Fi reconnect<br>

17. Mesh snippet:<br>
```python
ttl2 = ttl - 1
if ttl2 < 0:
	return
```
Why this matters?<br>
A. Enforces bounded propagation and prevents endless forwarding<br>
B. Increases payload size<br>
C. Changes ORIG field<br>
D. Forces scan restart<br>

18. Mesh snippet:<br>
```python
if typ == 'R':
	return
```
In assignment context this usually means:<br>
A. Do not forward/count R packets in trigger pipeline<br>
B. Convert R to T<br>
C. Drop all packets<br>
D. Disable dedupe<br>

19. Mesh snippet:<br>
```python
self.ble.gap_advertise(ADV_INTERVAL_US, adv_data=payload)
self._adv_stop_ms = time.ticks_add(time.ticks_ms(), duration_ms)
```
Intent:<br>
A. Start timed advertise burst and stop later via service function<br>
B. Start permanent advertising forever<br>
C. Start active scan<br>
D. Reset node ID<br>

20. Multi-select. Mesh trigger logic (after 5 packets) should count only packets that are:<br>
A. New after dedupe<br>
B. Non-self origin<br>
C. Type not R<br>
D. Any packet with strongest RSSI only<br>

21. LoRa snippet:<br>
```cpp
rf95.send(buf, len);
rf95.waitPacketSent();
```
Why call waitPacketSent?<br>
A. Ensure TX is finished before proceeding to next timing logic<br>
B. Compute checksum<br>
C. Read RSSI<br>
D. Subscribe to topic<br>

22. LoRa RX snippet:<br>
```cpp
if (msg.destID != MY_ID && msg.destID != 0xFF) return;
```
Behavior:<br>
A. Accept packets for self or broadcast only<br>
B. Accept only broadcast<br>
C. Accept all packets<br>
D. Reject all ACK messages<br>

23. LoRa checksum snippet:<br>
```cpp
if (calculateChecksum(msg) == msg.checksum) sendAck(msg.nodeID);
else sendNack(msg.nodeID);
```
Best interpretation:<br>
A. Integrity gate drives ACK/NACK branch<br>
B. Security authentication<br>
C. Compression ratio check<br>
D. OLED rendering path<br>

24. LoRa TX snippet:<br>
```cpp
while (!ackReceived && retries < MAX_RETRIES) {
  sendMessage(&msg);
  ackReceived = waitForAck(ACK_TIMEOUT);
  if (!ackReceived) retries++;
}
```
Core reliability mechanism is:<br>
A. timeout-based retry with cap<br>
B. random frequency hopping built-in<br>
C. automatic TLS handshake<br>
D. BLE fallback mode<br>

25. Multi-select. OLED helper snippet in LoRa files is mainly for:<br>
A. runtime status visibility during demo/debug<br>
B. replacing serial entirely in all environments<br>
C. showing setup/send/wait/receive/error phases<br>
D. increasing RF signal strength<br>

### Snippet-Style Mixed Mock Answers
1. A
2. B
3. B
4. A
5. A,B,D
6. A
7. B
8. A
9. A
10. A,B,D
11. A
12. A
13. A
14. A
15. A,B,C
16. A
17. A
18. A
19. A
20. A,B,C
21. A
22. A
23. A
24. A
25. A,C
