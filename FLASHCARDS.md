# CSC2106 MCQ / Multi-Select Flashcards

Format: **Q** = question, **A** = answer(s), **E** = explanation

---

## Lab 1 — RESTful API

**Q1.** Which HTTP method should be used to retrieve the current temperature from the Pico W?
- A) POST
- B) PUT
- C) GET ✓
- D) DELETE

**E:** GET is the read-only method. The `/temp` endpoint only accepts GET.

---

**Q2.** A client sends `POST /led` with body `{"state": 2}`. What does the server return?
- A) 200 OK, LED turns ON
- B) 200 OK, LED turns OFF
- C) 400 Bad Request ✓
- D) 404 Not Found

**E:** `_parse_led_state()` only accepts `0` or `1`. Any other value returns `None`, which triggers a 400.

---

**Q3.** Which file in the REST lab handles Wi-Fi connection and the main server loop?
- A) `web_server.py`
- B) `main.py` ✓
- C) `simple.py`
- D) `router.py`

---

**Q4.** What HTTP status code does the server return when a client sends `GET /led`?
- A) 200 OK
- B) 400 Bad Request
- C) 404 Not Found
- D) 405 Method Not Allowed ✓

**E:** `/led` only accepts POST. A GET to it returns 405.

---

**Q5.** What `Content-Type` header must the `GET /status` response include? (Assignment)
- A) `text/html`
- B) `text/plain`
- C) `application/json` ✓
- D) `application/xml`

---

**Q6.** What value does `serve_client()` return when handling `GET /temp`?
- A) `1`
- B) `0`
- C) `-1` ✓
- D) The temperature value

**E:** `-1` means "no LED change". Only `/led` POST changes the LED state.

---

**Q7.** Which of the following are valid body formats for `POST /led`? (Multi-select)
- A) `{"state": 1}` ✓
- B) `state=1` ✓
- C) `1` ✓
- D) `led=on`

**E:** The parser handles JSON, form-encoded, and plain text. `led=on` is not a supported format.

---

**Q8.** The Pico W REST server listens on which port?
- A) 8080
- B) 443
- C) 1883
- D) 80 ✓

---

## Lab 2 — MQTT

**Q9.** Which QoS level requires an acknowledgement from the broker and guarantees delivery at least once?
- A) QoS 0
- B) QoS 1 ✓
- C) QoS 2
- D) QoS 3

---

**Q10.** Who publishes the Last Will and Testament (LWT) message?
- A) The client, when it disconnects gracefully
- B) The client, when it starts up
- C) The broker, when the client disconnects unexpectedly ✓
- D) Another subscribed client

---

**Q11.** Which topic(s) should use retained messages? (Multi-select)
- A) `csc2106/devA/status` ✓
- B) `csc2106/led/cmd`
- C) `csc2106/devB/status` ✓
- D) `csc2106/led/ack`

**E:** State topics (online/offline status) use retained. Event topics (commands, ACKs) do not.

---

**Q12.** Pico A presses a button on GP21. What does it publish?
- A) `TOGGLE` to `csc2106/led/cmd` ✓
- B) `ACK` to `csc2106/led/ack`
- C) `TOGGLE` to `csc2106/devA/status`
- D) `HELLO` to `csc2106/led/cmd`

---

**Q13.** In the assignment, Node B receives a message on `csc2106/nodeB/led/cmd`. What should it do?
- A) Publish an ACK and do nothing else
- B) Toggle its onboard LED ✓
- C) Disconnect from the broker
- D) Republish to `csc2106/nodeA/led/cmd`

---

**Q14.** Which library does the Pico W use for MQTT communication?
- A) `paho.mqtt`
- B) `umqtt.simple` ✓
- C) `mqtt.client`
- D) `micropython.mqtt`

---

**Q15.** A new client subscribes to `csc2106/devB/status`. What does it immediately receive?
- A) Nothing, it has to wait for the next publish
- B) The last retained message on that topic ✓
- C) All historical messages on that topic
- D) The LWT message

**E:** Retained messages are sent immediately to new subscribers.

---

**Q16.** What is the default port for the Mosquitto MQTT broker?
- A) 80
- B) 8883
- C) 1883 ✓
- D) 5672

---

**Q17.** Which statements about QoS 0 are true? (Multi-select)
- A) No acknowledgement is required ✓
- B) The broker stores messages until delivery ✓ (for offline clients)
- C) Messages may be lost ✓
- D) It is the heaviest QoS level

---

**Q18.** In the Mosquitto v2 config for this lab, what two lines are needed? (Multi-select)
- A) `listener 1883` ✓
- B) `allow_anonymous true` ✓
- C) `password_file /etc/mosquitto/passwd`
- D) `protocol websockets`

---

## Lab 3 — BLE Flooding Mesh

**Q19.** What is the correct frame format used in the BLE mesh lab?
- A) `ORIG|MSGID|TTL|TYPE|DATA`
- B) `M1|ORIG|MSGID|TTL|TYPE|DATA` ✓
- C) `TYPE|ORIG|TTL|DATA`
- D) `MSGID|M1|ORIG|TTL|DATA`

---

**Q20.** In Part 2 of the mesh lab, what problem occurs with only two nodes?
- A) Nodes cannot discover each other
- B) Messages are dropped after 3 hops
- C) Packets bounce back and forth indefinitely (ping-pong) ✓
- D) TTL prevents any forwarding

---

**Q21.** Which two mechanisms are added in Part 3 to make flooding stable? (Multi-select)
- A) De-duplication using `(ORIG, MSGID)` ✓
- B) TTL decrement on forward ✓
- C) Encryption of the payload
- D) Periodic scan restart

---

**Q22.** A node injects a message. Why must it immediately mark that message as "seen"?
- A) To prevent the broker from receiving it twice
- B) To stop it from forwarding its own message when it hears it bounce back ✓
- C) To reset the injection timer
- D) Because the BLE stack requires it

---

**Q23.** What does `TTL = 0` mean when a node receives a message?
- A) Forward it immediately
- B) Do not forward it — message stops here ✓
- C) Inject a new message
- D) Delete the seen cache

---

**Q24.** How long does an advertise burst last in this lab?
- A) 100 ms
- B) 200 ms
- C) 300 ms ✓
- D) 1000 ms

---

**Q25.** In the assignment, which messages must NOT be counted toward the "5 new messages" trigger? (Multi-select)
- A) Duplicate messages (already in seen cache) ✓
- B) Messages from the node itself ✓
- C) Messages with `TYPE = 'R'` ✓
- D) Messages with `TYPE = 'T'`

---

**Q26.** What `TTL` value should assignment `R` messages use?
- A) 3 (DEFAULT_TTL)
- B) 1
- C) 0 ✓
- D) -1

**E:** TTL=0 means the message will not be forwarded beyond the immediate receiver.

---

**Q27.** This BLE mesh lab uses BLE Mesh as defined by the Bluetooth SIG.
- A) True
- B) False ✓

**E:** It is an application-layer flooding relay using advertisements — not the official BLE Mesh spec.

---

**Q28.** What is the purpose of jitter in injection scheduling?
- A) To make messages harder to intercept
- B) To prevent all nodes from injecting at the same time and colliding ✓
- C) To reduce TTL automatically
- D) To increase the advertise burst window

---

## Lab 4 — LoRa

**Q29.** What does LoRa stand for?
- A) Low Rate
- B) Long Range ✓
- C) Low Radio
- D) Linked Over Radio

---

**Q30.** What is the maximum range of LoRa in rural areas?
- A) 100 m
- B) 1 km
- C) 16 km ✓
- D) 160 km

---

**Q31.** Which frequency band does LoRa operate in?
- A) Licensed 5G band
- B) 2.4 GHz ISM (same as Wi-Fi)
- C) License-free sub-GHz ISM band ✓
- D) 60 GHz mmWave

---

**Q32.** Which library is used in the Arduino LoRa lab?
- A) LoRaWAN
- B) RadioHead ✓
- C) RF24
- D) TinyGSM

---

**Q33.** What is the topology used in the LoRa lab?
- A) Star (via gateway)
- B) Mesh
- C) Peer-to-peer (P2P) ✓
- D) Bus

---

**Q34.** Which of the following are assignment requirements for the LoRa lab? (Multi-select)
- A) Display status messages on OLED ✓
- B) Implement ACK and retransmit ✓
- C) ID-based messaging with header, payload, and checksum ✓
- D) Connect to a cloud dashboard

---

**Q35.** Which OLED library is specifically mentioned for the I2C display in the LoRa lab?
- A) U8g2
- B) Adafruit SSD1306 ✓
- C) TFT_eSPI
- D) OLED128x64

---

**Q36.** Compared to Wi-Fi, LoRa offers: (Multi-select)
- A) Much longer range ✓
- B) Lower power consumption ✓
- C) Higher data rates
- D) Operation in license-free bands ✓

---

## Lab 6 — BLE Point-to-Point

**Q37.** In BLE GATT, which device role advertises and accepts connections?
- A) Central / Client
- B) Peripheral / Server ✓
- C) Observer
- D) Broadcaster

---

**Q38.** What is the UUID of the LED State characteristic in this lab?
- A) `0x1A01`
- B) `0x1A02`
- C) `0x1A00` ✓
- D) `0x1800`

---

**Q39.** Which permissions does the Status (Temp/Volt) characteristic have? (Multi-select)
- A) READ ✓
- B) WRITE
- C) NOTIFY ✓
- D) INDICATE

---

**Q40.** The client presses Button B (GP1). What BLE operation does this trigger?
- A) `gattc_read()`
- B) `gattc_write()` ✓
- C) `gatts_notify()`
- D) `gap_scan()`

---

**Q41.** What IRQ event does the Server receive when the Client writes to a characteristic?
- A) `_IRQ_GATTC_NOTIFY` (18)
- B) `_IRQ_CENTRAL_CONNECT` (1)
- C) `_IRQ_GATTS_WRITE` (3) ✓
- D) `_IRQ_GATTC_READ_DONE` (15)

---

**Q42.** What is the key difference between a BLE Beacon and a GATT Peripheral?
- A) Beacons use higher power
- B) Beacons register GATT services; Peripherals do not
- C) Beacons only advertise and never allow connections ✓
- D) Peripherals cannot be discovered by scanners

---

**Q43.** A BLE Beacon is best suited for which use case?
- A) Transferring large files
- B) Location or presence signaling ✓
- C) Two-way data exchange
- D) Video streaming

---

**Q44.** After the Client disconnects from the Server, what does the Server do?
- A) Shuts down BLE
- B) Waits indefinitely for reconnection
- C) Starts advertising again ✓
- D) Resets all characteristic values

---

**Q45.** Which app can be used to verify a BLE Beacon is advertising? (Multi-select)
- A) nRF Connect ✓
- B) BLE Scanner ✓
- C) Postman
- D) Mosquitto

---

**Q46.** What does `gattc_write(..., 1)` — the last argument `1` — mean in this lab?
- A) Write with response (server ACKs)
- B) Write No Response ✓
- C) Notify the server
- D) Write to characteristic index 1

---

## Cross-Topic / Mixed

**Q47.** Which protocols use a publish/subscribe model? (Multi-select)
- A) MQTT ✓
- B) REST
- C) BLE GATT (NOTIFY) ✓
- D) LoRa P2P

---

**Q48.** Which IoT protocol is best suited for communicating over several kilometres with low power?
- A) REST over Wi-Fi
- B) MQTT over Wi-Fi
- C) BLE
- D) LoRa ✓

---

**Q49.** Which protocols require a broker or central relay to function? (Multi-select)
- A) MQTT ✓
- B) REST
- C) BLE GATT (P2P)
- D) BLE Flooding Mesh (via broadcast, no broker)

---

**Q50.** Which statement about de-duplication in the BLE Flooding Mesh is correct?
- A) It is keyed on `ORIG` alone
- B) It is keyed on `MSGID` alone
- C) It is keyed on `ORIG:MSGID` pair ✓
- D) It is keyed on `TTL:TYPE`

---

**Q51.** In MQTT, a retained message is sent to a new subscriber: (Multi-select)
- A) Immediately upon subscription ✓
- B) Without the client needing to wait for the next publish ✓
- C) Only if the client requests it explicitly
- D) Only once ✓

---

**Q52.** Rank these protocols from longest to shortest typical range:
- A) LoRa > Wi-Fi/MQTT > BLE ✓
- B) BLE > LoRa > Wi-Fi
- C) Wi-Fi > LoRa > BLE
- D) LoRa > BLE > Wi-Fi

---

**Q53.** Which of the following are true about BLE advertising in the mesh lab? (Multi-select)
- A) No BLE connection is established ✓
- B) Data is carried in the device name field of the advertisement ✓
- C) Messages are sent using GATT characteristics
- D) Any nearby BLE scanner can receive the advertisement ✓

---

**Q54.** In the REST lab, what happens if `Content-Type: application/json` is set but the body is `{"state": 5}`?
- A) LED is set to ON (truthy)
- B) LED is set to OFF (falsy)
- C) Server returns 400 Bad Request ✓
- D) Server returns 404 Not Found

---

**Q55.** Which of the following correctly describes LWT in MQTT?
- A) A message the client sends when it connects
- B) A message the client sends when it disconnects cleanly
- C) A pre-configured message the broker publishes if the client disconnects unexpectedly ✓
- D) A message retained by the broker for 24 hours

---
