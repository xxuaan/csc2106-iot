# REST Pico W Lab MCQ Questions and Answers

## Section A: Core MCQs

<ol>
  <li>The primary purpose of `main.py` in this lab is to:<br>
    A. Parse JSON only<br>
    B. Manage Wi-Fi, socket loop, and hardware state updates<br>
    C. Provide BLE services<br>
    D. Compile static web pages<br>
  </li>
  <li>The primary purpose of `web_server.py` is to:<br>
    A. Read ADC continuously only<br>
    B. Handle HTTP parsing, routing, and response creation<br>
    C. Configure USB mass storage<br>
    D. Control PWM audio<br>
  </li>
  <li>The server listens on which port by default in this lab?<br>
    A. 21<br>
    B. 80<br>
    C. 1883<br>
    D. 443<br>
  </li>
  <li>The endpoint that returns current temperature is typically:<br>
    A. GET /status<br>
    B. POST /temp<br>
    C. GET /temp<br>
    D. PUT /temp<br>
  </li>
  <li>The endpoint used to control LED state is:<br>
    A. GET /led<br>
    B. POST /led<br>
    C. DELETE /led<br>
    D. PATCH /led<br>
  </li>
  <li>If a client uses GET on `/led` but the route expects POST, correct status is:<br>
    A. 200<br>
    B. 404<br>
    C. 405<br>
    D. 500<br>
  </li>
  <li>Unknown route should return:<br>
    A. 201<br>
    B. 400<br>
    C. 405<br>
    D. 404<br>
  </li>
  <li>For assignment route `/status`, expected content type is:<br>
    A. text/html<br>
    B. application/json<br>
    C. text/plain only<br>
    D. multipart/form-data<br>
  </li>
  <li>Which field should represent LED state in `/status` response?<br>
    A. led_pin<br>
    B. led_value_raw<br>
    C. led_on (boolean)<br>
    D. led_port<br>
  </li>
  <li>Why is `json.dumps(...)` used for `/status`?<br>
    A. To connect Wi-Fi<br>
    B. To convert Python dict into JSON string<br>
    C. To open socket<br>
    D. To parse URL path<br>
  </li>
  <li>Which body is valid for turning LED on in JSON mode?<br>
    A. {"state":"on"}<br>
    B. {"state":1}<br>
    C. {"led":true,"x":9}<br>
    D. {"value":"high"}<br>
  </li>
  <li>In this lab flow, hardware LED update is finally applied by:<br>
    A. HTTP client browser<br>
    B. main loop after interpreting returned state<br>
    C. DNS server<br>
    D. Thonny editor<br>
  </li>
  <li>What does `0.0.0.0` bind address mean here?<br>
    A. Listen only on localhost<br>
    B. Listen on all network interfaces<br>
    C. Disable network stack<br>
    D. Connect to default gateway<br>
  </li>
  <li>A 400 Bad Request on `/led` most likely means:<br>
    A. Wi-Fi disconnected<br>
    B. Route not implemented<br>
    C. Request body/state format invalid<br>
    D. Wrong IP subnet mask<br>
  </li>
  <li>Why pass current LED state into `serve_client(...)` for `/status`?<br>
    A. So server can report actual runtime actuator state<br>
    B. To speed Wi-Fi handshake<br>
    C. To reduce ADC noise<br>
    D. To avoid socket bind<br>
  </li>
  <li>What is the benefit of separating `main.py` and `web_server.py`?<br>
    A. Reduces code readability<br>
    B. Clean separation of hardware loop and HTTP routing logic<br>
    C. Prevents use of status codes<br>
    D. Removes need for parsing<br>
  </li>
  <li>The Pico W temperature value in this lab comes from:<br>
    A. External DHT22 only<br>
    B. ADC read of internal temperature sensor path<br>
    C. HTTP header<br>
    D. Wi-Fi RSSI<br>
  </li>
  <li>If route exists but method is unsupported, which is correct?<br>
    A. 404<br>
    B. 302<br>
    C. 405<br>
    D. 418<br>
  </li>
  <li>The expected `/status` JSON includes:<br>
    A. humidity and pressure only<br>
    B. temperature and led_on<br>
    C. ip and mac only<br>
    D. ssid and password<br>
  </li>
  <li>Why validate LED state to only 0/1?<br>
    A. To minimize flash usage only<br>
    B. To enforce deterministic actuator behavior and reject malformed input<br>
    C. To increase TCP window size<br>
    D. To avoid JSON parsing<br>
  </li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="21">
  <li>`GET /status` returns HTML but should be JSON. Most direct fix is:<br>
    A. Change port to 443<br>
    B. Build dict, `json.dumps`, and return with `content_type="application/json"`<br>
    C. Remove LED endpoint<br>
    D. Switch to BLE notifications<br>
  </li>
  <li>`POST /led` returns 200 but physical LED does not change. Most likely architecture issue:<br>
    A. Browser cache problem<br>
    B. main loop does not apply returned LED state to hardware<br>
    C. Wrong DNS record<br>
    D. Missing root route<br>
  </li>
  <li>Client gets 405 on `/temp`. Interpretation:<br>
    A. Path exists, method used is not allowed for that path<br>
    B. Path does not exist<br>
    C. JSON is invalid<br>
    D. Socket is closed permanently<br>
  </li>
  <li>JSON payload `{ "state": "1" }` is sent. Robust parser behavior should:<br>
    A. Reject all string values always<br>
    B. Optionally coerce "1" to int if explicitly supported<br>
    C. Turn LED on and off simultaneously<br>
    D. Return 404<br>
  </li>
  <li>Server handles only first request then appears stuck. Most plausible low-level cause:<br>
    A. Socket not listening<br>
    B. Client socket not closed in handler path<br>
    C. Temperature conversion too accurate<br>
    D. Missing SSID<br>
  </li>
  <li>Unknown path returns 200 with root page. Why is this undesirable?<br>
    A. It hides routing errors; correct behavior should be explicit 404<br>
    B. It improves API discoverability<br>
    C. It is required by REST<br>
    D. It prevents timeout<br>
  </li>
  <li>Assignment asks `led_on` boolean in `/status`, but code returns `0/1` numeric. Best fix:<br>
    A. Keep as integer; booleans are invalid JSON<br>
    B. Convert to Python bool before JSON serialization<br>
    C. Convert to string "true"/"false" manually always<br>
    D. Remove LED field<br>
  </li>
  <li>Malformed `/led` payload causes unhandled exception and crash. Best defensive improvement:<br>
    A. Wrap parse in validation + exception handling and return 400<br>
    B. Remove request body support<br>
    C. Force reboot after each request<br>
    D. Ignore all POST requests<br>
  </li>
  <li>Two clients send rapid `/led` POST requests with opposite values. Most accurate statement:<br>
    A. Last successfully processed request determines final LED state<br>
    B. First request is always permanent<br>
    C. Pico automatically merges states<br>
    D. HTTP forbids concurrent updates<br>
  </li>
  <li>If `/status` should reflect actual LED state, the value should be sourced from:<br>
    A. Last request body only<br>
    B. Hardware read/current runtime LED value passed from main loop<br>
    C. Wi-Fi driver statistics<br>
    D. Socket peer address<br>
  </li>
  <li>Returning 500 for user input validation failures is poor practice because:<br>
    A. 500 indicates server fault, not client request format issues<br>
    B. 500 is faster than 400<br>
    C. 500 enables better parsing<br>
    D. 500 is required for POST<br>
  </li>
  <li>If content type is `application/x-www-form-urlencoded`, expected LED body shape is typically:<br>
    A. `{state:1}`<br>
    B. `state=1`<br>
    C. `<state>1</state>`<br>
    D. `LED ON`<br>
  </li>
  <li>Main loop catches OSError and continues. Primary benefit:<br>
    A. Keeps server resilient to transient connection/socket errors<br>
    B. Avoids all parsing code<br>
    C. Eliminates need for status codes<br>
    D. Forces non-blocking mode automatically<br>
  </li>
  <li>Best quick end-to-end verification for assignment requirement:<br>
    A. Only open `/` in browser<br>
    B. POST `/led` then GET `/status` and verify JSON + LED truth value match<br>
    C. Restart Pico after each request<br>
    D. Disable Wi-Fi encryption<br>
  </li>
  <li>Most accurate split of responsibilities:<br>
    A. `web_server.py` handles hardware writes; `main.py` handles HTML only<br>
    B. `main.py` orchestrates hardware and loop; `web_server.py` handles HTTP request/response routing<br>
    C. Both files do identical work<br>
    D. `main.py` is optional<br>
  </li>
</ol>

## Section C: Code-Trace MCQs + Short Answers

### C1. Code-Trace MCQs
<ol>
  <li>POST /led returns 200 but LED never changes. Most likely missing code step is:<br>
    A. Root route handler<br>
    B. Applying returned LED state in main loop<br>
    C. Temperature read call<br>
    D. Wi-Fi reconnect<br>
  </li>
  <li>GET /status returns HTML instead of JSON. Which change is required?<br>
    A. Change port to 443<br>
    B. Build dict and return with content_type application/json<br>
    C. Disable LED endpoint<br>
    D. Change SSID<br>
  </li>
  <li>Client sends invalid LED payload and server crashes. Best fix location is:<br>
    A. In socket bind block<br>
    B. In LED parse/route branch with guarded parsing<br>
    C. In Wi-Fi connect<br>
    D. In serial print<br>
  </li>
  <li>Method is wrong for existing path, correct status should be:<br>
    A. 404<br>
    B. 405<br>
    C. 500<br>
    D. 302<br>
  </li>
  <li>Unknown path requested by client should route to:<br>
    A. 200 root response<br>
    B. 404 not found branch<br>
    C. 405 method branch<br>
    D. Sensor read branch<br>
  </li>
</ol>

### C2. Short-Answer Bank

1. Why pass current_led_value into serve_client?
2. Explain when to return 400 vs 405 vs 404 in this API.
3. Why centralize hardware LED write in main loop rather than deep inside parser?
4. Why set Content-Type explicitly on /status response?
5. What is risk of simplistic recv(1024) parser assumption?

## Answers (All Sections)

### Section A
<ol>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>C</li>
  <li>D</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section B
<ol start="21">
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section C
<ol>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
</ol>

### Section C2
1. Status endpoint must reflect actual actuator state at request time; passing runtime LED value avoids stale state assumptions.
2. 400 for malformed body/input, 405 when method is unsupported for existing route, 404 for unknown path.
3. Keeps separation of concerns: web handler decides command intent, main loop applies hardware actuation consistently.
4. Clients parse behavior by MIME type; application/json is required for machine-readable API contract.
5. Large or fragmented HTTP requests may not be fully captured in one recv call; parser may miss body completeness in edge cases.

