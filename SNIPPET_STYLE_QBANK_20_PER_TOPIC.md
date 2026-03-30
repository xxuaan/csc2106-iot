# Snippet-Style Question Bank (20 Per Topic)

Focus: lab-test style, no coding required, but deep code understanding.

## BLE Snippet-Style (20)

1. Snippet:<br>
```python
self._ble.gattc_write(conn, cccd_handle, b'\x01\x00', 0)
```
Primary effect?<br>
A. Enables notifications for this connection<br>
B. Writes LED OFF<br>
C. Starts scan<br>
D. Disconnects peripheral<br>

2. Snippet:<br>
```python
if event == _IRQ_GATTS_WRITE:
    conn_handle, value_handle = data
```
This branch indicates:<br>
A. Client wrote to server characteristic<br>
B. Scan finished<br>
C. Notify delivered<br>
D. Service discovery done<br>

3. Snippet:<br>
```python
self._ble.gap_advertise(200000, adv_data=payload)
```
What is this doing?<br>
A. Start periodic advertising<br>
B. Start scanning<br>
C. Write CCCD<br>
D. Read characteristic<br>

4. Snippet:<br>
```python
self._ble.gap_scan(2000, 30000, 30000)
```
Likely intent:<br>
A. Scan for nearby advertisers for 2 seconds<br>
B. Advertise for 2 seconds<br>
C. Disconnect after 2 seconds<br>
D. Enable notifications<br>

5. Snippet:<br>
```python
if bytes(addr) == self._target_addr:
    self._ble.gap_scan(None)
    self._ble.gap_connect(addr_type, addr)
```
Why stop scan before connect?<br>
A. Avoid state conflicts and race conditions<br>
B. Required for checksum validation<br>
C. Increase RSSI<br>
D. Enable write-no-response<br>

6. Snippet:<br>
```python
elif event == _IRQ_PERIPHERAL_CONNECT:
    self._ble.gattc_discover_services(conn_handle)
```
Why discover services immediately?<br>
A. Need runtime handle ranges before char discovery<br>
B. To re-advertise<br>
C. To set LED output<br>
D. To parse notify payload<br>

7. Snippet:<br>
```python
elif event == _IRQ_GATTC_DESCRIPTOR_RESULT:
    if uuid == bluetooth.UUID(0x2902):
        self._cccd_handle = dsc_handle
```
What is being found?<br>
A. CCCD descriptor handle<br>
B. Service UUID<br>
C. LED handle<br>
D. Connection interval<br>

8. Snippet:<br>
```python
self._ble.gattc_read(self._conn_handle, self._led_handle)
```
Where does value arrive?<br>
A. _IRQ_GATTC_READ_RESULT event<br>
B. Immediate return value<br>
C. _IRQ_SCAN_RESULT<br>
D. _IRQ_GATTS_WRITE<br>

9. Snippet:<br>
```python
if value_handle == self._led_handle:
    raw = self._ble.gatts_read(value_handle)
```
Server side meaning:<br>
A. Process write to LED characteristic<br>
B. Process notify from client<br>
C. Process scan result<br>
D. Process disconnect<br>

10. Snippet:<br>
```python
for ch in self._connections:
    self._ble.gatts_notify(ch, self._status_handle)
```
Why iterate connection list?<br>
A. Notify every active client<br>
B. Increase advertising speed<br>
C. Find service UUID<br>
D. Reconnect Wi-Fi<br>

11. Snippet:<br>
```python
if now - last_press > 500:
```
Main purpose:<br>
A. Debounce button input<br>
B. BLE keepalive<br>
C. MTU negotiation<br>
D. Descriptor discovery<br>

12. Snippet:<br>
```python
self._led_handle = handles[0][0]
self._status_handle = handles[0][1]
```
Why store these?
A. Future read/write/notify operations need runtime handles<br>
B. To set GPIO pin numbers<br>
C. To compute checksum<br>
D. To format JSON<br>

13. Snippet:<br>
```python
if self._conn_handle is not None and self._led_handle is not None:
```
This guard prevents:<br>
A. Invalid BLE operations before connection/discovery<br>
B. High RSSI<br>
C. Duplicate ACK<br>
D. Route mismatch<br>

14. Snippet:<br>
```python
self._ble.gattc_write(conn, led_handle, b'1', 1)
```
`1` mode in this context often means:<br>
A. Write with response requested<br>
B. Notification enable<br>
C. Read request<br>
D. Disconnect request<br>

15. Snippet:<br>
```python
self._ble.gattc_write(conn, led_handle, b'1', 0)
```
`0` mode usually means:<br>
A. Write without response (lower overhead)<br>
B. Scan mode<br>
C. Advertise mode<br>
D. Notify mode<br>

16. Snippet:<br>
```python
elif event == _IRQ_PERIPHERAL_DISCONNECT:
    self._reset_handles()
    self.scan()
```
Best interpretation:<br>
A. Recover client state and rescan after disconnect<br>
B. Restart server advertising<br>
C. Publish MQTT status<br>
D. Recompute ADC temp<br>

17. Snippet:<br>
```python
if event == _IRQ_GATTC_NOTIFY and value_handle == self._status_handle:
```
Why check `value_handle`?
A. Ensure notify is from expected characteristic<br>
B. Decode UUID bytes<br>
C. Restart scan<br>
D. Set LED pin<br>

18. Snippet:<br>
```python
name = decode_name_from_adv(adv_data)
if name == 'LX_SERVER':
```
This supports:
A. Target filtering during scan<br>
B. CCCD write<br>
C. Characteristic read<br>
D. Service registration<br>

19. Snippet:<br>
```python
self._ble.gap_scan(None)
```
In MicroPython BLE scan API this usually:
A. Stops current scan<br>
B. Starts default scan<br>
C. Starts advertising<br>
D. Clears descriptors<br>

20. Snippet:<br>
```python
except Exception:
    pass
```
In server write parse branch, better alternative is:<br>
A. Validate + log malformed payload instead of silent ignore<br>
B. Reboot immediately<br>
C. Disable notifications<br>
D. Remove LED characteristic<br>

### BLE Answers
1. A
2. A
3. A
4. A
5. A
6. A
7. A
8. A
9. A
10. A
11. A
12. A
13. A
14. A
15. A
16. A
17. A
18. A
19. A
20. A

## REST Snippet-Style (20)

1. Snippet:<br>
```python
s.bind(('0.0.0.0', 80))
```
Meaning:<br>
A. Listen on all interfaces at HTTP port 80<br>
B. Localhost only<br>
C. MQTT bind<br>
D. TLS-only bind<br>

2. Snippet:<br>
```python
client, addr = s.accept()
```
This call does what?
A. Waits for incoming TCP client connection<br>
B. Sends HTTP response<br>
C. Parses JSON<br>
D. Reads ADC<br>

3. Snippet:<br>
```python
temp_c = read_temperature()
```
Typical source in this lab:<br>
A. ADC(4) internal sensor path<br>
B. BLE characteristic<br>
C. MQTT topic<br>
D. External DHT only<br>

4. Snippet:<br>
```python
if path == '/temp' and method == 'GET':
```
This is an example of:
A. Route + method dispatch<br>
B. Socket reconnect<br>
C. GPIO debounce<br>
D. QoS negotiation<br>

5. Snippet:<br>
```python
if path == '/led' and method != 'POST':
    status = 405
```
Why 405?
A. Route exists but method not allowed<br>
B. Unknown path<br>
C. Malformed body<br>
D. Server crash<br>

6. Snippet:<br>
```python
if path not in routes:
    status = 404
```
404 indicates:
A. Unknown endpoint<br>
B. Wrong method on known endpoint<br>
C. Invalid body<br>
D. Timeout<br>

7. Snippet:<br>
```python
resp = http_response(body, status=200, content_type='application/json')
```
Why set JSON content-type?
A. Clients parse response as machine-readable JSON<br>
B. Faster Wi-Fi<br>
C. Required by socket accept<br>
D. Enables BLE notify<br>

8. Snippet:<br>
```python
data = json.loads(body)
state = data.get('state')
```
Main risk if unguarded:
A. Exception on malformed JSON<br>
B. Frequency mismatch<br>
C. Duplicate ACK<br>
D. Wrong UUID<br>

9. Snippet:<br>
```python
elif ct == 'application/x-www-form-urlencoded':
```
This branch handles:
A. Form body like state=1<br>
B. Binary BLE packet<br>
C. MQTT topic parsing<br>
D. I2C display data<br>

10. Snippet:<br>
```python
return -1
```
In serve_client LED command pattern, `-1` means:
A. No LED state change requested<br>
B. LED ON<br>
C. LED OFF<br>
D. Invalid route always<br>

11. Snippet:<br>
```python
if new_led_state == 1:
    onboard_led.value(1)
```
Why apply in main loop?
A. Keep hardware side effects centralized<br>
B. Avoid HTTP parsing<br>
C. Disable route logic<br>
D. Required by Wi-Fi stack<br>

12. Snippet:<br>
```python
parts = raw.split('\r\n\r\n', 1)
```
Purpose:
A. Separate headers block from body<br>
B. Separate method from path only<br>
C. Validate Wi-Fi credentials<br>
D. Parse BLE descriptors<br>

13. Snippet:<br>
```python
method, path, _ = request_line.split(' ', 2)
```
This extracts:
A. HTTP method and path from request line<br>
B. JSON keys<br>
C. Socket address<br>
D. ADC voltage<br>

14. Snippet:<br>
```python
if state not in (0,1):
    return http_response(..., status=400)
```
Why 400?
A. Client sent invalid input format/value<br>
B. Unknown route<br>
C. Wrong method<br>
D. Internal server error<br>

15. Snippet:<br>
```python
status_obj = {'temperature': temp, 'led_on': bool(led)}
```
Most likely endpoint:
A. GET /status<br>
B. POST /led<br>
C. GET /<br>
D. GET /favicon.ico<br>

16. Snippet:<br>
```python
except OSError:
    continue
```
Main intent:
A. Keep server alive through transient socket errors<br>
B. Disable Wi-Fi<br>
C. Force reboot<br>
D. Clear all routes<br>

17. Snippet:<br>
```python
if method == 'POST' and path == '/led':
```
Best conceptual mapping:
A. state-changing endpoint branch<br>
B. read-only endpoint<br>
C. background task scheduler<br>
D. BLE event handler<br>

18. Snippet:<br>
```python
headers.get('Content-Type','').split(';')[0].strip().lower()
```
Why normalize like this?
A. Robust matching despite charset suffix/case differences<br>
B. Increase ADC precision<br>
C. Avoid socket bind<br>
D. Enable QoS1<br>

19. Snippet:<br>
```python
conn.send(response)
conn.close()
```
This follows common simple-server pattern:<br>
A. one request per connection<br>
B. persistent websocket<br>
C. MQTT retain publish<br>
D. BLE notify sequence<br>

20. Snippet:<br>
```python
if len(raw) == 0:
    return
```
This protects against:
A. Empty/closed client reads<br>
B. Unknown endpoint<br>
C. Method mismatch<br>
D. Invalid JSON only<br>

### REST Answers
1. A
2. A
3. A
4. A
5. A
6. A
7. A
8. A
9. A
10. A
11. A
12. A
13. A
14. A
15. A
16. A
17. A
18. A
19. A
20. A

## MQTT Snippet-Style (20)

1. Snippet:<br>
```python
client = MQTTClient(CLIENT_ID, BROKER, keepalive=30)
```
`keepalive=30` mainly helps:
A. Detect stale broker sessions<br>
B. Increase payload size<br>
C. Enable BLE<br>
D. Remove need for callback<br>

2. Snippet:<br>
```python
client.set_last_will(STATUS_TOPIC, b'offline', retain=True, qos=1)
```
Effect:
A. Broker can publish offline state on unexpected disconnect<br>
B. Forces online state<br>
C. Auto-subscribes command topic<br>
D. Disables duplicates<br>

3. Snippet:<br>
```python
client.set_callback(on_msg)
```
Without what call in loop, callback will not run in umqtt.simple?
A. client.check_msg()<br>
B. client.publish()<br>
C. client.subscribe() every cycle<br>
D. wifi_connect() every cycle<br>

4. Snippet:<br>
```python
client.subscribe(CMD_TOPIC, qos=1)
```
This means node will:
A. receive messages published to CMD_TOPIC<br>
B. publish retain status automatically<br>
C. disable LWT<br>
D. parse HTTP body<br>

5. Snippet:<br>
```python
if topic == CMD_TOPIC and msg == b'TOGGLE':
    led.value(0 if led.value() else 1)
```
Why topic+payload check both?
A. prevent accidental action from unrelated traffic<br>
B. required by broker auth<br>
C. decode RSSI<br>
D. start scan<br>

6. Snippet:<br>
```python
client.publish(STATUS_TOPIC, b'online', retain=True, qos=1)
```
Why retain=True here?
A. late subscribers immediately see latest state<br>
B. command replay<br>
C. disable callback<br>
D. remove keepalive<br>

7. Snippet:<br>
```python
client.publish(CMD_TOPIC, b'TOGGLE', qos=1)
```
QoS1 implication:
A. at-least-once delivery, duplicates possible<br>
B. exactly-once guaranteed<br>
C. no ACK path<br>
D. local-only delivery<br>

8. Snippet:<br>
```python
except Exception:
    client = make_client()
```
Purpose:
A. reconnect/rebuild session after failures<br>
B. increase debounce<br>
C. reset LED pin mode<br>
D. clear retained topics globally<br>

9. Snippet:<br>
```python
btn_now = (btn.value() == 0)
if btn_now and not btn_prev:
```
This pattern is:
A. edge detection for button press<br>
B. QoS dedupe<br>
C. topic wildcard<br>
D. json parsing<br>

10. Snippet:<br>
```python
if topic == CMD_TO_A_TOPIC:
```
In assignment nodeA, this represents:
A. subscribe-side command handling for A<br>
B. publish-side command send to B<br>
C. status topic for LWT<br>
D. ack topic only<br>

11. Snippet:<br>
```python
client_id = b'NodeA'
```
Why unique client_id matters?
A. duplicate IDs may cause broker to drop one connection<br>
B. enables retain<br>
C. sets qos<br>
D. sets topic prefix automatically<br>

12. Snippet:<br>
```python
client.publish(ACK_TOPIC, b'ON' if led.value() else b'OFF', qos=1)
```
This is used for:
A. acknowledging actuator outcome/status<br>
B. enabling subscriptions<br>
C. changing keepalive<br>
D. resetting broker<br>

13. Snippet:<br>
```python
client.check_msg()
time.sleep(0.02)
```
Why small sleep?
A. reduce busy-loop CPU while polling frequently<br>
B. disable callback<br>
C. increase qos<br>
D. reconnect delay only<br>

14. Snippet:<br>
```python
if topic != expected_topic:
    return
```
Main reason:
A. filter irrelevant subscribed traffic<br>
B. speed Wi-Fi connect<br>
C. set LWT<br>
D. disable retain<br>

15. Snippet:<br>
```python
publish(cmd_topic, retain=False)
publish(status_topic, retain=True)
```
Why this split?
A. commands are transient, status is latest state snapshot<br>
B. broker requires it by protocol<br>
C. QoS1 forbids retain on status<br>
D. retained messages cannot be bytes<br>

16. Snippet:<br>
```python
client.connect()
client.subscribe(...)
```
After reconnect, why subscribe again?
A. subscriptions may not persist for new clean session<br>
B. to increase LED brightness<br>
C. to avoid LWT<br>
D. to decode JSON<br>

17. Snippet:<br>
```python
if msg == b'TOGGLE':
```
If payload typo is `b'toggle'`, likely behavior:
A. no action due to strict match<br>
B. auto-correct and toggle anyway<br>
C. broker converts case<br>
D. callback not called<br>

18. Snippet:<br>
```python
wifi_connect()
client = make_client()
```
Order rationale:
A. MQTT depends on network availability first<br>
B. MQTT can connect before Wi-Fi<br>
C. Wi-Fi optional for MQTT<br>
D. required by BLE stack<br>

19. Snippet:<br>
```python
client.publish(STATUS_TOPIC, b'offline', retain=True, qos=1)
```
In normal graceful shutdown, this could be used to:
A. explicitly mark node offline<br>
B. subscribe to offline topic<br>
C. clear broker memory<br>
D. disable keepalive<br>

20. Snippet:<br>
```python
if not wlan.isconnected():
    wlan.connect(...)
```
Why check before connect attempts?
A. avoid unnecessary reconnect calls when already connected<br>
B. improve qos level<br>
C. clear retained topics<br>
D. decode messages<br>

### MQTT Answers
1. A
2. A
3. A
4. A
5. A
6. A
7. A
8. A
9. A
10. A
11. A
12. A
13. A
14. A
15. A
16. A
17. A
18. A
19. A
20. A

## Mesh Snippet-Style (20)

1. Snippet:<br>
```python
frame = 'M1|{}|{}|{}|{}|{}'.format(orig, msgid, ttl, typ, data)
```
This defines:
A. application-level frame format<br>
B. BLE service registration<br>
C. MQTT topic schema<br>
D. HTTP route map<br>

2. Snippet:<br>
```python
parts = s.split('|', 5)
```
Why split with max fields?
A. parse fixed frame structure robustly<br>
B. encrypt payload<br>
C. restart scan<br>
D. improve RSSI<br>

3. Snippet:<br>
```python
if not s.startswith('M1|'):
    return None
```
Purpose:
A. reject non-protocol payloads early<br>
B. enable forwarding<br>
C. increase ttl<br>
D. advertise continuously<br>

4. Snippet:<br>
```python
if key in self.seen:
    return True
self.seen.append(key)
```
Implements:
A. dedupe cache insertion/check<br>
B. checksum<br>
C. endpoint routing<br>
D. ack retry<br>

5. Snippet:<br>
```python
if len(self.seen) > SEEN_MAX:
    del self.seen[0:len(self.seen)-SEEN_MAX]
```
Why trim?
A. bound memory growth<br>
B. increase hop count<br>
C. disable duplicates<br>
D. set node ID<br>

6. Snippet:<br>
```python
ttl2 = ttl - 1
if ttl2 < 0:
    return
```
Main role:
A. stop forwarding after hop budget exhausted<br>
B. increase payload<br>
C. set RSSI threshold<br>
D. connect to server<br>

7. Snippet:<br>
```python
self.ble.gap_advertise(ADV_INTERVAL_US, adv_data=payload)
self._adv_active = True
```
This starts:
A. advertise burst<br>
B. scan<br>
C. MQTT publish<br>
D. HTTP response<br>

8. Snippet:<br>
```python
if self._adv_active and ticks_diff(now, self._adv_stop_ms) >= 0:
    self.ble.gap_advertise(None)
```
This does:
A. stop advertise at burst end time<br>
B. start scan<br>
C. reset seen cache<br>
D. increment ttl<br>

9. Snippet:<br>
```python
self.ble.gap_scan(SCAN_MS, 30000, 30000)
```
Likely meaning:
A. active/persistent scan window with configured interval/window params<br>
B. start advertising<br>
C. register service<br>
D. write descriptor<br>

10. Snippet:<br>
```python
elif event == _IRQ_SCAN_DONE:
    self.scan()
```
Why restart here?
A. keep node continuously listening over time<br>
B. flush dedupe<br>
C. force injection<br>
D. disable forwarding<br>

11. Snippet:<br>
```python
if orig == NODE_ID:
    return
```
This avoids:
A. processing own-origin packets as external traffic<br>
B. parsing frame<br>
C. starting scan<br>
D. setting ttl<br>

12. Snippet:<br>
```python
if typ == 'R':
    return
```
Assignment intent:
A. do not count/forward response type in trigger logic<br>
B. convert to T<br>
C. disable scanner<br>
D. clear buffer<br>

13. Snippet:<br>
```python
if len(rx_buffer) == 5:
    inject_R()
```
This is:
A. event-driven trigger on received-traffic threshold<br>
B. fixed timer injection<br>
C. random jitter scheduler<br>
D. ttl reset condition<br>

14. Snippet:<br>
```python
msgid = time.ticks_ms() & 0xFFFFFFFF
```
Why mask 32-bit?
A. stable bounded ID format for frame field<br>
B. improve RSSI<br>
C. convert to UUID<br>
D. encrypt message<br>

15. Snippet:<br>
```python
name = frame_to_name(frame)
```
Reason this helper is needed:
A. adv name payload has practical length constraints<br>
B. BLE requires JSON<br>
C. disables dedupe<br>
D. sets node ID randomly<br>

16. Snippet:<br>
```python
if seen_check_add(key):
    return
```
This means:
A. duplicates are dropped before further handling<br>
B. duplicates are prioritized<br>
C. ttl reset to max<br>
D. start connect<br>

17. Snippet:<br>
```python
forward_ttl(orig, msgid, ttl, typ, payload)
```
Forwarded frame should keep what unchanged?
A. orig and msgid (identity)<br>
B. ttl and payload must both change<br>
C. only typ changes<br>
D. node_id becomes local node always<br>

18. Snippet:<br>
```python
next_inject = now + INJECT_PERIOD + random_jitter
```
Why jitter?
A. reduce synchronized collisions among nodes<br>
B. force duplicate packets<br>
C. disable trigger logic<br>
D. increase ttl<br>

19. Snippet:<br>
```python
if parse_frame(s) is None:
    return
```
Best practice represented:
A. fail-fast on malformed input<br>
B. auto-correct malformed frames<br>
C. ignore ttl checks<br>
D. bypass dedupe<br>

20. Snippet:<br>
```python
seen_check_add(f'{NODE_ID}:{msgid}')
```
When injecting own frame, this helps:
A. prevent self-bounce re-forward loops<br>
B. increase advertise power<br>
C. shorten payload<br>
D. avoid scan restart<br>

### Mesh Answers
1. A
2. A
3. A
4. A
5. A
6. A
7. A
8. A
9. A
10. A
11. A
12. A
13. A
14. A
15. A
16. A
17. A
18. A
19. A
20. A

## LoRa Snippet-Style (20)

1. Snippet:<br>
```cpp
if (!rf95.init()) { ... }
```
This checks:
A. radio module initialization success<br>
B. OLED font<br>
C. JSON parse<br>
D. Wi-Fi connect<br>

2. Snippet:<br>
```cpp
rf95.setFrequency(915.5);
```
Critical implication:
A. peer node must use matching frequency<br>
B. checksum must be disabled<br>
C. ACK no longer needed<br>
D. OLED address changes<br>

3. Snippet:<br>
```cpp
rf95.send(buf, len);
rf95.waitPacketSent();
```
Pattern means:
A. send then block until TX complete<br>
B. receive then parse<br>
C. compute checksum<br>
D. restart module<br>

4. Snippet:<br>
```cpp
if (rf95.waitAvailableTimeout(ACK_TIMEOUT))
```
Purpose:
A. bounded wait for incoming ACK/response<br>
B. force transmit retries<br>
C. set tx power<br>
D. read OLED<br>

5. Snippet:<br>
```cpp
if (msg.destID != MY_ID && msg.destID != 0xFF) return;
```
This implements:
A. destination filtering with broadcast support<br>
B. checksum validation<br>
C. ack dedupe<br>
D. oled clear<br>

6. Snippet:<br>
```cpp
msg.checksum = calculateChecksum(msg);
```
Why assign before send?
A. receiver can validate integrity<br>
B. encrypt payload<br>
C. set msg type<br>
D. set frequency<br>

7. Snippet:<br>
```cpp
if (calc == msg.checksum) sendAck(msg.nodeID); else sendNack(msg.nodeID);
```
This branch is:
A. integrity-driven ACK/NACK decision<br>
B. RSSI-driven routing<br>
C. OLED mode switch<br>
D. SPI reset<br>

8. Snippet:<br>
```cpp
while (!ackReceived && retries < MAX_RETRIES)
```
Core reliability strategy:
A. retry with cap<br>
B. infinite retransmit<br>
C. no timeout logic<br>
D. random frequency hops<br>

9. Snippet:<br>
```cpp
displayStatus("Waiting for Reply", false);
```
Best reason for OLED status updates:
A. immediate runtime observability<br>
B. increase RF range<br>
C. replace checksum<br>
D. parse packet headers<br>

10. Snippet:<br>
```cpp
if (len < 5) return false;
```
Parser safety meaning:
A. minimum frame size guard<br>
B. ttl check<br>
C. node id rewrite<br>
D. ACK enable<br>

11. Snippet:<br>
```cpp
if (msg.payloadLen > 40) return false;
```
Why needed?
A. prevent payload buffer overrun<br>
B. increase throughput<br>
C. disable retries<br>
D. parse RSSI<br>

12. Snippet:<br>
```cpp
memcpy(&buf[idx], msg.payload, msg.payloadLen);
```
Correct only if:
A. payloadLen is validated against bounds<br>
B. checksum disabled<br>
C. destination is broadcast<br>
D. tx power maxed<br>

13. Snippet:<br>
```cpp
sendAck(msg.nodeID);
```
Why target `msg.nodeID`?
A. ACK goes back to original sender<br>
B. ACK always broadcast<br>
C. ACK to self<br>
D. ACK to fixed gateway<br>

14. Snippet:<br>
```cpp
int rssi = rf95.lastRssi();
```
Used mainly for:
A. link quality observation/debug<br>
B. checksum calculation<br>
C. node addressing<br>
D. OLED init<br>

15. Snippet:<br>
```cpp
if (!ackReceived) retries++;
```
What would be a bug here?
A. forgetting increment causes possible infinite loop<br>
B. incrementing too early disables send<br>
C. incrementing changes frequency<br>
D. incrementing clears OLED<br>

16. Snippet:<br>
```cpp
msg.msgType = MSG_DATA;
```
Protocol role:
A. distinguish packet semantics at app layer<br>
B. set SPI mode<br>
C. set GPIO mode<br>
D. set RSSI threshold<br>

17. Snippet:<br>
```cpp
msg.destID = DEST_NODE_ID;
```
If this is wrong, likely symptom:
A. receiver ignores packets due to filter mismatch<br>
B. checksum always passes<br>
C. OLED shows gibberish only<br>
D. rf95.init fails<br>

18. Snippet:<br>
```cpp
display.begin(SSD1306_SWITCHCAPVCC, 0x3C)
```
Common issue if wrong address used:
A. no OLED output despite running code<br>
B. no LoRa packets<br>
C. wrong checksum<br>
D. wrong node id<br>

19. Snippet:<br>
```cpp
if (msg.msgType == MSG_ACK)
```
TX-side importance:
A. confirm expected response type before declaring success<br>
B. parse payload only<br>
C. skip destination check<br>
D. disable retries<br>

20. Snippet:<br>
```cpp
delay(3000);
```
In assignment TX loop this often provides:
A. pacing between send cycles<br>
B. ACK encryption window<br>
C. parser synchronization<br>
D. descriptor discovery<br>

### LoRa Answers
1. A
2. A
3. A
4. A
5. A
6. A
7. A
8. A
9. A
10. A
11. A
12. A
13. A
14. A
15. A
16. A
17. A
18. A
19. A
20. A
