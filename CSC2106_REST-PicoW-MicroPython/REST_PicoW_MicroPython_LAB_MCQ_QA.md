

> For a much larger code-focused question set, use [MEGA_CODE_UNDERSTANDING_QBANK.md](../MEGA_CODE_UNDERSTANDING_QBANK.md) or the root file in this repo.

## Lab Test Format Alignment
- Focus: no coding required.
- Question type: MCQ / multi-select style conceptual understanding.
- Count: 20 questions (practice set).

## Section A: Lab-Test Style Questions (20)

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

## Answers
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


