# MQTT Pico W Lab MCQ Questions and Answers

## Section A: Core MCQs

<ol>
  <li>MQTT uses which communication model?<br>
    A. Client-server request-response only<br>
    B. Publish-subscribe via broker<br>
    C. Peer-to-peer without routing<br>
    D. Token-ring polling<br>
  </li>
  <li>Default Mosquitto MQTT port used in this lab is:<br>
    A. 80<br>
    B. 443<br>
    C. 1883<br>
    D. 3306<br>
  </li>
  <li>In MQTT, the broker is responsible for:<br>
    A. Toggling GPIO directly<br>
    B. Routing messages by topic<br>
    C. Creating Wi-Fi credentials<br>
    D. Compiling firmware<br>
  </li>
  <li>QoS 1 means:<br>
    A. At most once<br>
    B. At least once with acknowledgement<br>
    C. Exactly once always<br>
    D. No acknowledgement path<br>
  </li>
  <li>LWT is published when:<br>
    A. Client unsubscribes normally<br>
    B. Broker receives malformed JSON<br>
    C. Client disconnects unexpectedly<br>
    D. Topic is retained<br>
  </li>
  <li>Retained messages are best for:<br>
    A. One-time debug spam<br>
    B. Last known state for late subscribers<br>
    C. Firmware binary transfer<br>
    D. Button debounce<br>
  </li>
  <li>In this repo, command topics are typically:<br>
    A. Status topics with retain true<br>
    B. Event topics like `.../led/cmd`<br>
    C. Wi-Fi SSID strings<br>
    D. Broker config files<br>
  </li>
  <li>In `umqtt.simple`, callback for inbound messages is set by:<br>
    A. `set_last_will(...)`<br>
    B. `set_callback(...)`<br>
    C. `publish(...)`<br>
    D. `rf95.recv(...)`<br>
  </li>
  <li>Non-blocking receive in loop is typically done with:<br>
    A. `connect()` repeatedly<br>
    B. `check_msg()`<br>
    C. `subscribe()` every cycle<br>
    D. `set_callback()` every cycle<br>
  </li>
  <li>If publisher and subscriber topics differ by one path segment, likely result is:<br>
    A. Automatic wildcard delivery<br>
    B. No message received by intended node<br>
    C. Broker crash<br>
    D. Duplicate ACK loops<br>
  </li>
  <li>Bidirectional assignment requires each node to be:<br>
    A. Publisher only<br>
    B. Subscriber only<br>
    C. Both publisher and subscriber<br>
    D. Broker and client together<br>
  </li>
  <li>Status topics should usually be retained because:<br>
    A. They are high-frequency events<br>
    B. New subscribers need latest state immediately<br>
    C. It reduces keepalive<br>
    D. It disables duplicates<br>
  </li>
  <li>ACK topics are usually not retained because:<br>
    A. ACK is transient event data<br>
    B. Broker cannot retain short payloads<br>
    C. QoS 1 forbids retain<br>
    D. Clients cannot subscribe to ACK<br>
  </li>
  <li>Keepalive in MQTT mainly helps:<br>
    A. ADC conversion speed<br>
    B. Detect stale/broken sessions<br>
    C. Encrypt payloads<br>
    D. Reduce SSID length<br>
  </li>
  <li>Best response to publish/check exceptions in this lab pattern is:<br>
    A. Ignore forever<br>
    B. Disconnect if needed and reconnect client<br>
    C. Reflash firmware immediately<br>
    D. Clear topic list only<br>
  </li>
</ol>

## Section B: Harder Scenario MCQs

<ol start="16">
  <li>Node A publishes `TOGGLE` with QoS 1; Node B toggles twice unexpectedly once in a while. Most accurate explanation:<br>
    A. QoS 1 can produce duplicates; handler should be idempotent or deduplicated<br>
    B. QoS 1 guarantees exactly once<br>
    C. Broker rewrites payloads<br>
    D. Retain always causes doubles<br>
  </li>
  <li>After reconnect, Node B no longer responds to commands. Most likely missed step:<br>
    A. Re-subscribing topic after reconnect<br>
    B. Reformatting SSD1306<br>
    C. Re-reading ADC<br>
    D. Reinstalling Thonny<br>
  </li>
  <li>Monitoring client starts late and instantly sees `offline` for Node A before any new publish. Why?<br>
    A. Topic wildcard bug<br>
    B. Retained LWT message stored by broker<br>
    C. ACK replay<br>
    D. Wi-Fi driver cache<br>
  </li>
  <li>Node receives messages but callback never fires. Most plausible cause:<br>
    A. `check_msg()` not called in loop<br>
    B. `publish()` used QoS 1<br>
    C. Broker on port 1883<br>
    D. LED pin output mode<br>
  </li>
  <li>Both nodes publish to same command topic and subscribe same topic without filtering source. Key risk:<br>
    A. Message echo/feedback toggling loops<br>
    B. Impossible subscription<br>
    C. Broker cannot retain statuses<br>
    D. Keepalive timeout disabled<br>
  </li>
  <li>Using retain on `.../led/cmd` causes odd behavior on reboot. Why?<br>
    A. Retained command replays to new subscriber and retriggers action<br>
    B. Broker deletes messages<br>
    C. QoS becomes 0<br>
    D. LWT disabled<br>
  </li>
  <li>Node shows connected to Wi-Fi but cannot reach broker IP. First practical check:<br>
    A. Verify broker IP/subnet reachability and same network segment<br>
    B. Increase LED brightness<br>
    C. Change client ID length<br>
    D. Remove callback<br>
  </li>
  <li>Two devices use same MQTT client ID simultaneously. Typical effect:<br>
    A. Both coexist safely always<br>
    B. Broker may disconnect one when other connects<br>
    C. Topics merge automatically<br>
    D. Retain is disabled<br>
  </li>
  <li>Button press occasionally sends many commands. Most direct fix:<br>
    A. Add edge/debounce logic around button input<br>
    B. Use QoS 0<br>
    C. Disable pull-up<br>
    D. Remove keepalive<br>
  </li>
  <li>Best reason to keep status and command topics separate:<br>
    A. Avoid semantic mixing of state vs event traffic<br>
    B. Required by broker protocol<br>
    C. Needed for Wi-Fi association<br>
    D. Reduces flash writes only<br>
  </li>
</ol>

## Section C: Code-Trace MCQs + Short Answers

### C1. Code-Trace MCQs
<ol>
  <li>Node reconnects after error but stops reacting to commands. Most likely missed step is:<br>
    A. Recreate button pin<br>
    B. Re-subscribe topic and keep callback active after reconnect<br>
    C. Disable retain<br>
    D. Change client ID each loop<br>
  </li>
  <li>Both nodes connected, publish seems successful, but other side never toggles. First code check is:<br>
    A. Topic string exact match between publish and subscribe<br>
    B. ADC formula<br>
    C. OLED height<br>
    D. Serial baud<br>
  </li>
  <li>LWT does not appear on monitor after sudden power loss. Most likely issue:<br>
    A. Missing set_last_will configuration before connect<br>
    B. QoS set to 1<br>
    C. Button debounce too long<br>
    D. Wrong LED pin<br>
  </li>
  <li>Messages arrive but callback not executing. Most likely loop issue:<br>
    A. check_msg not called regularly<br>
    B. publish too frequent<br>
    C. keepalive too low only<br>
    D. status topic retained<br>
  </li>
  <li>Late subscriber receives old command and toggles unexpectedly. Most likely design mistake:<br>
    A. Command topic was published retained<br>
    B. Status topic retained<br>
    C. QoS 1 used<br>
    D. Topic had slash<br>
  </li>
</ol>

### C2. Short-Answer Bank

1. Why are status topics retained but command/ack topics usually not retained?
2. Explain reconnect-safe sequence after MQTT exception.
3. Why can QoS1 toggle logic produce duplicate visible effects?
4. What role does check_msg play in umqtt.simple architecture?
5. Why unique client IDs matter?

## Answers (All Sections)

### Section A
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

### Section B
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

### Section C
<ol>
  <li>B</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
  <li>A</li>
</ol>

### Section C2
1. Status represents latest state that late subscribers need immediately; command/ack are transient events and replaying them can trigger stale actions.
2. Disconnect old client if possible, recreate client, set callback, reconnect, re-subscribe control topics, re-publish retained online state.
3. QoS1 guarantees at-least-once, so duplicate delivery can occur; pure toggle actions are non-idempotent and may need dedupe tokens.
4. It polls socket for inbound packets and dispatches callback; without it, subscriptions appear dead.
5. Broker uses client ID as session identity; duplicate IDs can cause one client connection to drop when another connects.

