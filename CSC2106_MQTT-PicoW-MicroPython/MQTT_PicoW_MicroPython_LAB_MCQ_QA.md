# MQTT Pico W Lab MCQ Questions and Answers

## Section A: Core MCQs

<ol>
  <li>MQTT uses which communication model?<br>
    A. Client-server request-response only<br>
    B. Publish-subscribe via broker<br>
    C. Peer-to-peer without routing<br>
    D. Token-ring polling
  </li>
  <li>Default Mosquitto MQTT port used in this lab is:<br>
    A. 80<br>
    B. 443<br>
    C. 1883<br>
    D. 3306
  </li>
  <li>In MQTT, the broker is responsible for:<br>
    A. Toggling GPIO directly<br>
    B. Routing messages by topic<br>
    C. Creating Wi-Fi credentials<br>
    D. Compiling firmware
  </li>
  <li>QoS 1 means:<br>
    A. At most once<br>
    B. At least once with acknowledgement<br>
    C. Exactly once always<br>
    D. No acknowledgement path
  </li>
  <li>LWT is published when:<br>
    A. Client unsubscribes normally<br>
    B. Broker receives malformed JSON<br>
    C. Client disconnects unexpectedly<br>
    D. Topic is retained
  </li>
  <li>Retained messages are best for:<br>
    A. One-time debug spam<br>
    B. Last known state for late subscribers<br>
    C. Firmware binary transfer<br>
    D. Button debounce
  </li>
  <li>In this repo, command topics are typically:<br>
    A. Status topics with retain true<br>
    B. Event topics like `.../led/cmd`<br>
    C. Wi-Fi SSID strings<br>
    D. Broker config files
  </li>
  <li>In `umqtt.simple`, callback for inbound messages is set by:<br>
    A. `set_last_will(...)`<br>
    B. `set_callback(...)`<br>
    C. `publish(...)`<br>
    D. `rf95.recv(...)`
  </li>
  <li>Non-blocking receive in loop is typically done with:<br>
    A. `connect()` repeatedly<br>
    B. `check_msg()`<br>
    C. `subscribe()` every cycle<br>
    D. `set_callback()` every cycle
  </li>
  <li>If publisher and subscriber topics differ by one path segment, likely result is:<br>
    A. Automatic wildcard delivery<br>
    B. No message received by intended node<br>
    C. Broker crash<br>
    D. Duplicate ACK loops
  </li>
  <li>Bidirectional assignment requires each node to be:<br>
    A. Publisher only<br>
    B. Subscriber only<br>
    C. Both publisher and subscriber<br>
    D. Broker and client together
  </li>
  <li>Status topics should usually be retained because:<br>
    A. They are high-frequency events<br>
    B. New subscribers need latest state immediately<br>
    C. It reduces keepalive<br>
    D. It disables duplicates
  </li>
  <li>ACK topics are usually not retained because:<br>
    A. ACK is transient event data<br>
    B. Broker cannot retain short payloads<br>
    C. QoS 1 forbids retain<br>
    D. Clients cannot subscribe to ACK
  </li>
  <li>Keepalive in MQTT mainly helps:<br>
    A. ADC conversion speed<br>
    B. Detect stale/broken sessions<br>
    C. Encrypt payloads<br>
    D. Reduce SSID length
  </li>
  <li>Best response to publish/check exceptions in this lab pattern is:<br>
    A. Ignore forever<br>
    B. Disconnect if needed and reconnect client<br>
    C. Reflash firmware immediately<br>
    D. Clear topic list only
  </li>
</ol>

### Answers: Section A
<ol>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>B</li>
  <li>C</li>
  <li>B</li>
  <li>A</li>
  <li>B</li>
  <li>B</li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="16">
  <li>Node A publishes `TOGGLE` with QoS 1; Node B toggles twice unexpectedly once in a while. Most accurate explanation:<br>
    A. QoS 1 can produce duplicates; handler should be idempotent or deduplicated<br>
    B. QoS 1 guarantees exactly once<br>
    C. Broker rewrites payloads<br>
    D. Retain always causes doubles
  </li>
  <li>After reconnect, Node B no longer responds to commands. Most likely missed step:<br>
    A. Re-subscribing topic after reconnect<br>
    B. Reformatting SSD1306<br>
    C. Re-reading ADC<br>
    D. Reinstalling Thonny
  </li>
  <li>Monitoring client starts late and instantly sees `offline` for Node A before any new publish. Why?<br>
    A. Topic wildcard bug<br>
    B. Retained LWT message stored by broker<br>
    C. ACK replay<br>
    D. Wi-Fi driver cache
  </li>
  <li>Node receives messages but callback never fires. Most plausible cause:<br>
    A. `check_msg()` not called in loop<br>
    B. `publish()` used QoS 1<br>
    C. Broker on port 1883<br>
    D. LED pin output mode
  </li>
  <li>Both nodes publish to same command topic and subscribe same topic without filtering source. Key risk:<br>
    A. Message echo/feedback toggling loops<br>
    B. Impossible subscription<br>
    C. Broker cannot retain statuses<br>
    D. Keepalive timeout disabled
  </li>
  <li>Using retain on `.../led/cmd` causes odd behavior on reboot. Why?<br>
    A. Retained command replays to new subscriber and retriggers action<br>
    B. Broker deletes messages<br>
    C. QoS becomes 0<br>
    D. LWT disabled
  </li>
  <li>Node shows connected to Wi-Fi but cannot reach broker IP. First practical check:<br>
    A. Verify broker IP/subnet reachability and same network segment<br>
    B. Increase LED brightness<br>
    C. Change client ID length<br>
    D. Remove callback
  </li>
  <li>Two devices use same MQTT client ID simultaneously. Typical effect:<br>
    A. Both coexist safely always<br>
    B. Broker may disconnect one when other connects<br>
    C. Topics merge automatically<br>
    D. Retain is disabled
  </li>
  <li>Button press occasionally sends many commands. Most direct fix:<br>
    A. Add edge/debounce logic around button input<br>
    B. Use QoS 0<br>
    C. Disable pull-up<br>
    D. Remove keepalive
  </li>
  <li>Best reason to keep status and command topics separate:<br>
    A. Avoid semantic mixing of state vs event traffic<br>
    B. Required by broker protocol<br>
    C. Needed for Wi-Fi association<br>
    D. Reduces flash writes only
  </li>
</ol>

### Answers: Section B
<ol start="16">
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>B</li>
  <li>A</li>
  <li>A</li>
</ol>
