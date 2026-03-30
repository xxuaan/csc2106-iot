# Code Snippets Explained (With Annotations)

Detailed walkthrough of key code patterns in each lab with line-by-line explanations.

---

## BLE (Bluetooth Low Energy) - Pico W MicroPython

### Server Setup: Registering Services and Characteristics

**What it does:** Creates a BLE server that advertises a service with an LED control characteristic and a status characteristic.

```python
import bluetooth
from ble_advertising import advertising_payload

ble = bluetooth.BLE()
ble.active(True)

# Register the service and characteristics
(adv_handler, status_handle, led_handle) = ble.gatts_register_services(
    (
        (bluetooth.UUID(0x180A),
         ((bluetooth.UUID(0x2A29), bluetooth.FLAG_READ),
          (bluetooth.UUID(0x2A26), bluetooth.FLAG_READ),
          (bluetooth.UUID(0x2A27), bluetooth.FLAG_READ),
         )),
        (bluetooth.UUID(SERVICE_UUID),
         ((bluetooth.UUID(LED_CHAR_UUID), bluetooth.FLAG_WRITE),
          (bluetooth.UUID(STATUS_CHAR_UUID), bluetooth.FLAG_NOTIFY | bluetooth.FLAG_READ),
         )),
    )
)
```

**Breakdown:**
- `ble.gatts_register_services()`: Registers one or more BLE services on the server side.
- First tuple: Device Information Service (standard UUID 0x180A) with manufacturer/version info.
- Second tuple: Custom service (SERVICE_UUID) with two characteristics:
  - LED_CHAR_UUID: accepts WRITE commands (client controls LED)
  - STATUS_CHAR_UUID: supports NOTIFY and READ (server sends status updates to client)
- Returns **handles** (`adv_handler`, `status_handle`, `led_handle`) used later to read/write/notify these characteristics.

---

### Server: Starting Advertisement

**What it does:** Begins broadcasting the BLE service so nearby clients can discover it.

```python
payload = advertising_payload(
    name='LED_SERVER',
    services=[bluetooth.UUID(SERVICE_UUID)]
)

ble.gap_advertise(200000, adv_data=payload)
```

**Breakdown:**
- `advertising_payload()`: Helper function that builds the BLE advertisement packet (name + service UUIDs).
- `ble.gap_advertise(200000, adv_data=payload)`: Start advertising at 200ms interval with the payload.
  - Clients scanning nearby will see "LED_SERVER" and know this service is available.

---

### Server: IRQ Event Handler for Client Writes

**What it does:** Process incoming client commands (e.g., "turn LED on") and send status updates.

```python
_IRQ_GATTS_WRITE = 3
_IRQ_GATTS_NOTIFY_DONE = 4

def ble_irq(event, data):
    if event == _IRQ_GATTS_WRITE:
        conn_handle, value_handle = data
        print(f"Client wrote to characteristic {value_handle}")
        
        if value_handle == led_handle:
            # Read what the client wrote
            value = ble.gatts_read(value_handle)
            if value == b'1':
                onboard_led.on()
            elif value == b'0':
                onboard_led.off()
            
            # Send status back to all connected clients
            status_value = b'LED_ON' if onboard_led.is_on() else b'LED_OFF'
            ble.gatts_write(status_handle, status_value)
            ble.gatts_notify(conn_handle, status_handle)

ble.irq(ble_irq)
```

**Breakdown:**
- `_IRQ_GATTS_WRITE`: Event fires when server receives a write from a client.
- `ble.gatts_read(value_handle)`: Read what the client wrote to this characteristic.
- `ble.gatts_write(status_handle, status_value)`: Update the status characteristic value in the database.
- `ble.gatts_notify(conn_handle, status_handle)`: Send a notification to the connected client about the new value.

---

### Client: Scanning for Nearby Servers

**What it does:** Search for BLE devices advertising the target service.

```python
_IRQ_SCAN_RESULT = 5
_IRQ_SCAN_DONE = 6

def ble_irq(event, data):
    if event == _IRQ_SCAN_RESULT:
        addr_type, addr, adv_type, rssi, adv_data = data
        
        # Find the name in the advertisement
        name = decode_name(adv_data)
        if name == 'LED_SERVER':
            print(f"Found LED_SERVER at {addr}, RSSI={rssi}")
            ble.gap_scan(None)  # Stop scanning
            ble.gap_connect(addr_type, addr)  # Connect to it

ble.gap_scan(2000, 30000, 30000)  # Scan for 2 seconds
ble.irq(ble_irq)
```

**Breakdown:**
- `ble.gap_scan(2000, 30000, 30000)`: Start scanning for advertised devices for 2000ms.
- `_IRQ_SCAN_RESULT`: Event fires each time a nearby device is detected.
- `decode_name(adv_data)`: Extract the name from the advertisement packet.
- `ble.gap_scan(None)`: Stop scanning (None = stop).
- `ble.gap_connect(addr_type, addr)`: Connect to the remote server using its address.

---

### Client: Discovering Services and Characteristics

**What it does:** After connecting, find the handles of the LED and Status characteristics on the remote server.

```python
_IRQ_PERIPHERAL_CONNECT = 0
_IRQ_GATTC_SERVICE_RESULT = 8
_IRQ_GATTC_CHAR_RESULT = 9

def ble_irq(event, data):
    if event == _IRQ_PERIPHERAL_CONNECT:
        conn_handle, addr_type, addr = data
        print(f"Connected to {addr}")
        
        # Start discovering services
        ble.gattc_discover_services(conn_handle)
    
    elif event == _IRQ_GATTC_SERVICE_RESULT:
        conn_handle, start_handle, end_handle, uuid = data
        
        # Look for our custom service
        if str(uuid) == 'SERVICE_UUID':
            print(f"Found service, handles {start_handle}-{end_handle}")
            # Now discover characteristics within this range
            ble.gattc_discover_characteristics(conn_handle, start_handle, end_handle)
    
    elif event == _IRQ_GATTC_CHAR_RESULT:
        conn_handle, def_handle, value_handle, props, uuid = data
        
        # Store the value_handle for later read/write
        if str(uuid) == 'LED_CHAR_UUID':
            led_char_handle = value_handle
            print(f"Found LED characteristic at handle {value_handle}")
        elif str(uuid) == 'STATUS_CHAR_UUID':
            status_char_handle = value_handle
            print(f"Found Status characteristic at handle {value_handle}")

ble.irq(ble_irq)
```

**Breakdown:**
- `_IRQ_PERIPHERAL_CONNECT`: Event fires when client successfully connects to a server.
- `ble.gattc_discover_services()`: Ask the server "what services do you have?"
- `_IRQ_GATTC_SERVICE_RESULT`: Server responds with each service it advertises.
- `ble.gattc_discover_characteristics()`: Ask "what characteristics does this service have?"
- `_IRQ_GATTC_CHAR_RESULT`: Server responds with each characteristic; we save the `value_handle` for future operations.

---

### Client: Writing to LED Characteristic

**What it does:** Send a command to turn the server's LED on or off.

```python
# Write '1' to turn LED ON
ble.gattc_write(conn_handle, led_char_handle, b'1', 1)
# Mode 1 = write with response (waits for server ACK)

# Write '0' to turn LED OFF  
ble.gattc_write(conn_handle, led_char_handle, b'0', 0)
# Mode 0 = write without response (fire and forget, lower latency)
```

**Breakdown:**
- `ble.gattc_write(conn_handle, value_handle, data, mode)`:
  - `conn_handle`: Which server to write to.
  - `value_handle`: Which characteristic to write to (discovered earlier).
  - `data`: The payload (b'1' or b'0').
  - `mode=1`: Write with response (wait for ACK).
  - `mode=0`: Write without response (faster, but no confirmation).

---

### Client: Reading Status and Enabling Notifications

**What it does:** Read the current status and subscribe to status updates.

```python
# Read the current status
ble.gattc_read(conn_handle, status_char_handle)

# Enable notifications on status characteristic
# Step 1: Enable via CCCD (Client Characteristic Configuration Descriptor)
ble.gattc_write(conn_handle, cccd_handle, b'\x01\x00', 0)

_IRQ_GATTC_READ_RESULT = 11
_IRQ_GATTC_NOTIFY = 18

def ble_irq(event, data):
    if event == _IRQ_GATTC_READ_RESULT:
        conn_handle, value_handle, char_data = data
        print(f"Read from {value_handle}: {char_data}")
    
    elif event == _IRQ_GATTC_NOTIFY:
        conn_handle, value_handle, notify_data = data
        print(f"Notification from {value_handle}: {notify_data}")
```

**Breakdown:**
- `ble.gattc_read()`: Request current value from a characteristic (result in `_IRQ_GATTC_READ_RESULT` event).
- `ble.gattc_write(..., b'\x01\x00', 0)` to CCCD handle:
  - `b'\x01\x00'`: Magic bytes to enable notifications (little-endian 0x0001).
  - After this, server will push updates via `_IRQ_GATTC_NOTIFY` events.

---

## REST API - Pico W MicroPython

### Server: Socket Setup and Main Loop

**What it does:** Create a TCP server listening on port 80 (HTTP), accept client connections, and handle requests.

```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
s.bind(('0.0.0.0', 80))  # Listen on all interfaces, port 80
s.listen(1)

print("HTTP Server listening on port 80...")

while True:
    try:
        client, addr = s.accept()  # Wait for incoming connection
        print(f"Client connected from {addr}")
        
        # Handle the request (see next snippet)
        serve_client(client, addr)
        
        client.close()
    except Exception as e:
        print(f"Error: {e}")
```

**Breakdown:**
- `socket.socket()`: Create a TCP socket.
- `s.bind(('0.0.0.0', 80))`: Listen on all network interfaces on port 80 (HTTP standard port).
- `s.listen(1)`: Queue up to 1 connection.
- `client, addr = s.accept()`: Block until a client connects, then return the client socket and their address.
- `s.close()`: Always cleanup when done.

---

### Server: Parsing HTTP Request

**What it does:** Read the HTTP request, extract method/path/headers/body.

```python
def serve_client(client, addr):
    # Read raw request from client
    raw = client.recv(4096).decode('utf-8', errors='ignore')
    
    if not raw:
        return
    
    # Split headers from body
    parts = raw.split('\r\n\r\n', 1)
    headers_text = parts[0]
    body = parts[1] if len(parts) > 1 else ''
    
    # Parse first line: "GET /temp HTTP/1.1"
    lines = headers_text.split('\r\n')
    request_line = lines[0]
    method, path, protocol = request_line.split(' ', 2)
    
    # Parse remaining headers
    headers = {}
    for line in lines[1:]:
        if ':' in line:
            key, value = line.split(':', 1)
            headers[key.strip()] = value.strip()
    
    print(f"Method: {method}, Path: {path}")
    print(f"Headers: {headers}")
    print(f"Body: {body}")
```

**Breakdown:**
- `client.recv(4096)`: Read up to 4096 bytes of request data.
- `split('\r\n\r\n', 1)`: HTTP uses blank line (two CRLFs) to separate headers from body.
- `request_line.split(' ', 2)`: Extract "GET", "/temp", "HTTP/1.1".
- Parse remaining lines as `Key: Value` header pairs.

---

### Server: Routing and Handling GET /temp

**What it does:** Routes request to appropriate handler based on method + path, handles GET /temp to return current temperature.

```python
def serve_client(client, addr):
    # ... (parsing code from above)
    
    if method == 'GET':
        if path == '/temp':
            # Read temperature from built-in ADC
            temp_c = read_temperature()
            
            response_body = json.dumps({'temperature': temp_c, 'unit': 'celsius'})
            
            client.send(
                b'HTTP/1.1 200 OK\r\n'
                b'Content-Type: application/json\r\n'
                b'Content-Length: ' + str(len(response_body)).encode() + b'\r\n'
                b'\r\n' +
                response_body.encode()
            )
        
        elif path == '/led':
            # GET /led is not allowed (use POST instead)
            client.send(
                b'HTTP/1.1 405 Method Not Allowed\r\n'
                b'Content-Type: text/plain\r\n'
                b'\r\n'
                b'Use POST /led to control LED'
            )
        
        else:
            # Unknown path
            client.send(
                b'HTTP/1.1 404 Not Found\r\n'
                b'Content-Type: text/plain\r\n'
                b'\r\n'
                b'Endpoint not found'
            )
```

**Breakdown:**
- `read_temperature()`: Custom function to read Pico W's internal temperature sensor (via ADC(4)).
- `json.dumps({...})`: Convert Python dict to JSON string.
- **HTTP Response format:**
  - Status line: `HTTP/1.1 200 OK`
  - Headers (Content-Type, Content-Length)
  - Blank line: `\r\n\r\n`
  - Body: the JSON
- **Status codes:**
  - `200 OK`: Success.
  - `405 Method Not Allowed`: Route exists but this HTTP method not allowed.
  - `404 Not Found`: Unknown path.

---

### Server: Handling POST /led (State Change)

**What it does:** Accept a JSON body with LED state, validate it, control the LED, and respond.

```python
def serve_client(client, addr):
    # ... (parsing code from above)
    
    if method == 'POST':
        if path == '/led':
            # Check content type
            ct = headers.get('Content-Type', '').lower()
            
            try:
                if 'json' in ct:
                    # Parse JSON body
                    data = json.loads(body)
                    state = data.get('state')
                
                elif 'form-urlencoded' in ct:
                    # Parse form body (e.g., "state=1")
                    state = None
                    for pair in body.split('&'):
                        key, val = pair.split('=')
                        if key == 'state':
                            state = int(val)
                
                # Validate state
                if state not in (0, 1):
                    client.send(
                        b'HTTP/1.1 400 Bad Request\r\n'
                        b'Content-Type: text/plain\r\n'
                        b'\r\n'
                        b'state must be 0 or 1'
                    )
                else:
                    # Apply the state change
                    onboard_led.value(state)
                    
                    client.send(
                        b'HTTP/1.1 200 OK\r\n'
                        b'Content-Type: application/json\r\n'
                        b'\r\n'
                        + json.dumps({'led': state, 'status': 'ok'}).encode()
                    )
            
            except json.JSONDecodeError:
                client.send(
                    b'HTTP/1.1 400 Bad Request\r\n'
                    b'\r\n'
                    b'Invalid JSON'
                )
        else:
            # POST to unknown path
            client.send(b'HTTP/1.1 404 Not Found\r\n\r\nNot found')
```

**Breakdown:**
- Parse `Content-Type` header to know how to decode body (JSON vs form-urlencoded).
- `json.loads(body)`: Parse JSON string into Python dict.
- Form parsing: split by `&` then `=`.
- Validate `state` is 0 or 1; return `400 Bad Request` if not.
- `onboard_led.value(state)`: Apply the change.
- Return `200 OK` with confirmation JSON.

---

### Helper: Read Temperature from ADC

**What it does:** Get the Pico W's built-in temperature sensor reading.

```python
from machine import ADC

def read_temperature():
    # Pico W temperature sensor is on ADC channel 4
    adc = ADC(4)
    
    # Read raw ADC value (12-bit, 0-4095)
    raw = adc.read_u16()
    
    # Convert to voltage (reference is 3.3V, 16-bit max 65535)
    voltage = raw / 65535 * 3.3
    
    # Datasheet formula: Temp (°C) = 27 - (V - 0.706) / 0.001721
    temp_c = 27 - (voltage - 0.706) / 0.001721
    
    return round(temp_c, 1)
```

**Breakdown:**
- `ADC(4)`: Pico W's built-in temperature sensor is on ADC channel 4.
- `adc.read_u16()`: Read as 16-bit unsigned integer (0-65535).
- Convert to voltage: multiply by 3.3V and divide by max value.
- Apply datasheet formula to convert voltage to °C.

---

## MQTT - Pico W MicroPython

### Setup: Connect to Wi-Fi and Create MQTT Client

**What it does:** Establish Wi-Fi connection and create an MQTT client with will message (LWT).

```python
from umqtt.simple import MQTTClient
import network

# Connect to Wi-Fi
wlan = network.WLAN(network.STA_IF)
wlan.active(True)
wlan.connect('SSID', 'PASSWORD')

while not wlan.isconnected():
    print("Connecting...")
    time.sleep(0.1)

print(f"Connected. IP: {wlan.ifconfig()[0]}")

# Create MQTT client
CLIENT_ID = 'NodeA_' + str(time.time())[-6:]
BROKER = '192.168.1.100'  # Broker IP

client = MQTTClient(CLIENT_ID, BROKER, keepalive=30)

# Set Last Will and Testament (LWT)
# If this client suddenly disconnects, broker publishes this
client.set_last_will(
    'iot/status/nodeA',
    b'offline',
    retain=True,
    qos=1
)

# Connect to broker
client.connect()

# Publish "I'm online"
client.publish('iot/status/nodeA', b'online', retain=True, qos=1)

print("Connected to MQTT broker")
```

**Breakdown:**
- `MQTTClient()`: Create client with unique ID.
- `keepalive=30`: If no message every 30 seconds, broker considers connection dead.
- `set_last_will()`: Broker auto-publishes this message if connection drops unexpectedly.
- `publish(..., retain=True)`: Broker saves this message; new subscribers see it immediately.
- `publish(..., qos=1)`: At-least-once delivery (may be duplicated).

---

### Subscribe and Handle Messages

**What it does:** Subscribe to a command topic and process incoming messages.

```python
def on_message(topic, msg):
    """Called when a message arrives on a subscribed topic."""
    topic = topic.decode('utf-8') if isinstance(topic, bytes) else topic
    msg = msg.decode('utf-8') if isinstance(msg, bytes) else msg
    
    print(f"Message from {topic}: {msg}")
    
    if topic == 'iot/cmd/nodeA':
        if msg == 'TOGGLE':
            # Toggle LED
            current = onboard_led.value()
            onboard_led.value(1 - current)
            
            # Acknowledge the command
            client.publish('iot/ack/nodeA', b'TOGGLE_DONE', qos=1)
        
        elif msg == 'ON':
            onboard_led.value(1)
            client.publish('iot/ack/nodeA', b'ON_DONE', qos=1)
        
        elif msg == 'OFF':
            onboard_led.value(0)
            client.publish('iot/ack/nodeA', b'OFF_DONE', qos=1)

# Register the callback
client.set_callback(on_message)

# Subscribe to command topic
client.subscribe('iot/cmd/nodeA', qos=1)

# Main loop: check for messages
while True:
    client.check_msg()  # Non-blocking check for incoming messages
    time.sleep(0.05)
```

**Breakdown:**
- `set_callback(on_message)`: Register function to call when messages arrive.
- `subscribe(topic, qos=1)`: Tell broker "send me messages published to this topic".
- `client.check_msg()`: Non-blocking poll for new messages (calls callback if any arrived).
- Callback logic: check topic and payload, perform action, publish acknowledgment.

---

### Periodic Publishing (Status Updates)

**What it does:** Publish status periodically, with heartbeat/debounce logic.

```python
last_publish = 0
publish_interval = 5000  # milliseconds

button_prev = 0
btn_debounce = 200  # milliseconds

while True:
    now = time.ticks_ms()
    
    # Check button (hardware debounce)
    btn_now = (button.value() == 0)
    
    if btn_now and not button_prev and (now - last_btn_time) > btn_debounce:
        # Button pressed (rising edge detection with debounce)
        print("Button pressed, toggling LED...")
        
        current = onboard_led.value()
        onboard_led.value(1 - current)
        
        # Publish state change immediately
        state_msg = b'ON' if onboard_led.value() else b'OFF'
        client.publish('iot/status/nodeA_led', state_msg, retain=True, qos=1)
        
        last_btn_time = now
    
    button_prev = btn_now
    
    # Heartbeat: publish status every N seconds
    if now - last_publish > publish_interval:
        temp = read_temperature()
        payload = json.dumps({
            'temperature': temp,
            'led': bool(onboard_led.value()),
            'timestamp': now
        })
        
        client.publish('iot/status/nodeA', payload.encode(), retain=False, qos=1)
        last_publish = now
    
    # Always process incoming messages
    try:
        client.check_msg()
    except Exception as e:
        print(f"MQTT error: {e}")
        # Reconnect logic here (not shown)
    
    time.sleep(0.05)
```

**Breakdown:**
- **Button debounce:** Track previous state; only act on rising edge + time threshold.
- **Immediate publish:** When action taken (button press), publish right away.
- **Heartbeat:** Every N seconds, publish status regardless (shows device is alive).
- `retain=True` for status (clients need latest state), `retain=False` for transient updates.
- Always call `check_msg()` in loop to process incoming subscribed messages.

---

## BLE Flooding Mesh - Pico W MicroPython

### Message Frame Format and Parser

**What it does:** Define the mesh message structure and parse incoming frames.

```python
# Frame format: "M1|origin_id|msg_id|ttl|msg_type|payload"
# Example: "M1|node1|12345|5|T|TOGGLE"

def parse_frame(frame_str):
    """Parse mesh frame into components."""
    try:
        parts = frame_str.split('|', 5)  # Split into max 6 parts
        
        if len(parts) != 6 or parts[0] != 'M1':
            return None  # Invalid frame
        
        protocol_version = parts[0]
        origin = parts[1]
        msgid = parts[2]
        ttl = int(parts[3])
        msg_type = parts[4]
        payload = parts[5]
        
        return {
            'origin': origin,
            'msgid': msgid,
            'ttl': ttl,
            'type': msg_type,
            'payload': payload
        }
    
    except (ValueError, IndexError):
        return None  # Malformed frame

# Usage
frame = "M1|node1|54321|3|T|TOGGLE"
parsed = parse_frame(frame)
print(parsed)
# Output: {'origin': 'node1', 'msgid': '54321', 'ttl': 3, 'type': 'T', 'payload': 'TOGGLE'}
```

**Breakdown:**
- `split('|', 5)`: Split by `|` but keep at most 6 parts (payload may contain `|`).
- Validate protocol version is "M1".
- Extract each field.
- Return None if malformed (fail-fast).

---

### Dedupe Cache: Prevent Routing Loops

**What it does:** Track seen messages to avoid re-forwarding duplicates.

```python
class DedupeCache:
    def __init__(self, max_size=100):
        self.seen = []
        self.max_size = max_size
    
    def make_key(self, origin, msgid):
        """Create a unique key for this message."""
        return f"{origin}:{msgid}"
    
    def has_seen(self, origin, msgid):
        """Check if we've seen this message before."""
        key = self.make_key(origin, msgid)
        return key in self.seen
    
    def add(self, origin, msgid):
        """Mark this message as seen."""
        key = self.make_key(origin, msgid)
        
        if key not in self.seen:
            self.seen.append(key)
            
            # Trim if cache gets too large
            if len(self.seen) > self.max_size:
                # Keep only the most recent max_size entries
                self.seen = self.seen[-self.max_size:]

# Usage
cache = DedupeCache(max_size=50)

if not cache.has_seen('node1', '12345'):
    print("New message, process it")
    cache.add('node1', '12345')
else:
    print("Duplicate, ignore")
```

**Breakdown:**
- `make_key()`: Create unique string like "node1:12345".
- `has_seen()`: O(n) list search; for better performance, use set/dict in production.
- `add()`: Add to list and trim if it grows too large (simple memory management).
- Prevents infinite loops: if node A sends message that reaches node B, and node B forwards it back to A, A won't re-forward it.

---

### Forwarding Logic: Decrement TTL and Re-Advertise

**What it does:** When a message is received from another node, decrement TTL and broadcast to other nodes.

```python
def on_ble_scan(event, data):
    if event == _IRQ_SCAN_RESULT:
        addr_type, addr, adv_type, rssi, adv_data = data
        
        # Parse the advertisement name/data
        frame_str = parse_adv_data(adv_data)
        
        if not frame_str:
            return
        
        # Parse the frame
        frame = parse_frame(frame_str)
        
        if frame is None:
            return  # Malformed
        
        # Dedupe check
        if cache.has_seen(frame['origin'], frame['msgid']):
            return  # Already seen, don't forward
        
        cache.add(frame['origin'], frame['msgid'])
        
        # Don't process messages we originated
        if frame['origin'] == MY_NODE_ID:
            return
        
        # Handle special message types
        if frame['type'] == 'R':  # Response type
            return  # Don't count responses in trigger logic
        
        # Count as received traffic for trigger logic
        rx_buffer.append(frame)
        
        # Forward if TTL allows
        ttl = frame['ttl'] - 1  # Decrement
        
        if ttl > 0:
            # Re-advertise with lower TTL
            new_frame = f"M1|{frame['origin']}|{frame['msgid']}|{ttl}|{frame['type']}|{frame['payload']}"
            
            advertise_frame(new_frame)
            print(f"Forwarded: {new_frame}")
        else:
            print(f"TTL expired, not forwarding")

def advertise_frame(frame_str):
    """Convert frame to BLE advertisement and broadcast."""
    adv_name = frame_to_advertise_name(frame_str)
    
    ble.gap_advertise(
        ADV_INTERVAL_US,
        adv_data=frame_to_adv_data(adv_name)
    )
```

**Breakdown:**
- Check dedupe cache; return early if already seen.
- Don't forward if message originated from us (loop prevention).
- Ignore response types (to keep forwarding focused on queries/commands).
- **TTL decrement:** Each hop reduces TTL by 1. When TTL reaches 0, stop forwarding.
- Re-advertise with updated TTL, keeping origin and msgid unchanged (important for dedupe).

---

### Trigger Logic: React to Received Traffic

**What it does:** When enough messages are received, generate and inject an outgoing response.

```python
rx_buffer = []
RX_THRESHOLD = 5

last_inject_time = 0
INJECT_PERIOD = 3000  # milliseconds

while True:
    now = time.ticks_ms()
    
    # Check for trigger: if we've received multiple messages in short time
    if len(rx_buffer) >= RX_THRESHOLD and (now - last_inject_time) > INJECT_PERIOD:
        print(f"Trigger: received {len(rx_buffer)} traffic samples")
        
        # Generate an outgoing message
        msgid = time.ticks_ms() & 0xFFFFFFFF  # Use timestamp as unique ID
        
        frame = f"M1|{MY_NODE_ID}|{msgid}|5|R|OK"
        
        # Add small random jitter to avoid synchronized collisions
        jitter = random.randint(0, 500)
        
        advertise_frame(frame)
        
        cache.add(MY_NODE_ID, msgid)
        
        rx_buffer.clear()
        last_inject_time = now + jitter
    
    # Process BLE scan results (same irq as above)
    ble.gap_scan(SCAN_MS)
    
    time.sleep(0.01)
```

**Breakdown:**
- **Threshold check:** React only if we've received N messages.
- **Rate limiting:** Don't inject more than once per INJECT_PERIOD (avoid flooding).
- `msgid = time.ticks_ms() & 0xFFFFFFFF`: Use timestamp as unique message ID (32-bit mask for frame compatibility).
- **Jitter:** `random.randint(0, 500)` adds randomness so nearby nodes don't sync and collide.
- **Self-dedupe:** Immediately add our own generated message to cache so we don't re-forward it.

---

## LoRa - Arduino (C++)

### Radio Initialization

**What it does:** Set up the LoRa radio module with frequency, power, and other parameters.

```cpp
#include <RH_RF95.h>

// Pin definitions
#define RFM95_CS   10  // Chip select
#define RFM95_RST  9   // Reset
#define RFM95_INT  3   // Interrupt

RH_RF95 rf95(RFM95_CS, RFM95_INT);

void setup() {
    Serial.begin(9600);
    
    pinMode(RFM95_RST, OUTPUT);
    digitalWrite(RFM95_RST, HIGH);
    
    // Reset radio
    digitalWrite(RFM95_RST, LOW);
    delay(10);
    digitalWrite(RFM95_RST, HIGH);
    delay(10);
    
    // Initialize radio
    if (!rf95.init()) {
        Serial.println("LoRa init failed!");
        while (1) delay(1000);
    }
    
    // Set frequency (must match between RX and TX nodes)
    if (!rf95.setFrequency(915.5)) {
        Serial.println("Frequency setting failed!");
    }
    
    // Set transmission power (5-23 dBm)
    rf95.setTxPower(23, false);
    
    // Set spreading factor (6-12), lower = faster but shorter range
    rf95.setSpreadingFactor(7);
    
    Serial.println("LoRa radio initialized");
}
```

**Breakdown:**
- `RH_RF95_CS`/`INT`/`RST`: SPI chip select, interrupt, and reset pins.
- Hardware reset: pull RST low then high to reset the module.
- `rf95.init()`: Initialize radio; returns false if hardware not detected.
- `setFrequency(915.5)`: Both TX and RX must use same frequency (915 MHz common in US).
- `setTxPower(23, false)`: 23 dBm = max power (23 ≤ power ≤ 5), false = no high power mode.
- `setSpreadingFactor(7)`: SF7 = balanced range/speed (SF12 = longest range).

---

### Message Structure with Checksum

**What it does:** Define a message format and compute checksum for integrity checking.

```cpp
struct Message {
    uint8_t nodeID;      // Source node ID
    uint8_t destID;      // Destination (0xFF = broadcast)
    uint8_t msgType;     // Type: MSG_DATA, MSG_ACK, MSG_NACK
    uint16_t msgCounter; // Sequence number
    uint8_t payloadLen;  // How many bytes in payload
    uint8_t payload[40]; // Data (max 40 bytes)
    uint8_t checksum;    // XOR checksum for integrity
};

// Checksum: XOR all bytes
uint8_t calculateChecksum(Message &msg) {
    uint8_t checksum = 0;
    
    checksum ^= msg.nodeID;
    checksum ^= msg.destID;
    checksum ^= msg.msgType;
    checksum ^= (msg.msgCounter >> 8);   // High byte
    checksum ^= (msg.msgCounter & 0xFF); // Low byte
    checksum ^= msg.payloadLen;
    
    for (int i = 0; i < msg.payloadLen; i++) {
        checksum ^= msg.payload[i];
    }
    
    return checksum;
}

// Usage
Message msg;
msg.nodeID = 1;
msg.destID = 0xFF;  // Broadcast
msg.msgType = MSG_DATA;
msg.msgCounter = 0;
msg.payloadLen = 5;
strcpy((char*)msg.payload, "HELLO");

msg.checksum = calculateChecksum(msg);

Serial.println("Checksum: " + String(msg.checksum, HEX));
```

**Breakdown:**
- Each field is XOR'd into checksum.
- Multi-byte fields split into bytes (high/low).
- Simple XOR method (not cryptographic, just detects corruption).
- Receiver computes checksum again; if mismatch, corruption detected.

---

### Sending with ACK Timeout and Retry

**What it does:** Send a message and wait for acknowledgment, retry if timeout.

```cpp
#define MAX_RETRIES 3
#define ACK_TIMEOUT 1000  // milliseconds

bool sendMessageWithACK(Message &msg) {
    uint8_t retries = 0;
    
    while (retries < MAX_RETRIES) {
        // Serialize message to bytes and send
        uint8_t buf[sizeof(msg)] = {0};
        memcpy(buf, (uint8_t*)&msg, sizeof(msg));
        
        Serial.println("TX: Sending message...");
        displayStatus("Sending...", true);
        
        rf95.send(buf, sizeof(msg));
        rf95.waitPacketSent();  // Block until TX complete
        
        // Wait for ACK response
        if (rf95.waitAvailableTimeout(ACK_TIMEOUT)) {
            // Data received, read it
            uint8_t rxbuf[RH_RF95_MAX_MESSAGE_LEN] = {0};
            uint8_t len = sizeof(rxbuf);
            
            if (rf95.recv(rxbuf, &len)) {
                // Parse ACK
                Message ack;
                memcpy((uint8_t*)&ack, rxbuf, sizeof(ack));
                
                // Verify checksum
                uint8_t calc = calculateChecksum(ack);
                if (calc != ack.checksum) {
                    Serial.println("RX: Checksum mismatch!");
                    retries++;
                    continue;
                }
                
                // Verify it's an ACK for our message
                if (ack.msgType == MSG_ACK && ack.msgCounter == msg.msgCounter) {
                    Serial.println("RX: ACK received! Success!");
                    displayStatus("ACK OK", false);
                    return true;
                } else {
                    Serial.println("RX: Not our ACK, retrying...");
                    retries++;
                }
            }
        } else {
            // Timeout: no response
            Serial.println("TX: ACK timeout, retrying...");
            retries++;
        }
        
        delay(500);  // Wait before retry
    }
    
    Serial.println("TX: Max retries exceeded!");
    displayStatus("TX Failed", false);
    return false;
}
```

**Breakdown:**
- `memcpy()`: Copy struct into byte buffer for transmission.
- `rf95.send()` + `waitPacketSent()`: Send and block until radio finishes TX.
- `waitAvailableTimeout(1000)`: Wait up to 1000ms for response.
- `rf95.recv()`: Read response into buffer.
- Verify checksum and message type/counter to confirm it's for us.
- Retry logic: up to MAX_RETRIES attempts; sleep between retries to avoid collisions.

---

### Receiving and Sending ACK/NACK

**What it does:** Listen for incoming messages, validate, and reply with ACK or NACK.

```cpp
void receiveAndRespond() {
    if (rf95.available()) {
        // Data received
        uint8_t rxbuf[RH_RF95_MAX_MESSAGE_LEN] = {0};
        uint8_t len = sizeof(rxbuf);
        
        if (rf95.recv(rxbuf, &len)) {
            int rssi = rf95.lastRssi();
            Serial.println("RX: Message received (RSSI: " + String(rssi) + " dBm)");
            
            // Parse message
            Message msg;
            
            // Validate frame size
            if (len < sizeof(msg)) {
                Serial.println("RX: Too short!");
                return;
            }
            
            memcpy((uint8_t*)&msg, rxbuf, sizeof(msg));
            
            // Check destination
            if (msg.destID != MY_NODE_ID && msg.destID != 0xFF) {
                Serial.println("RX: Not for us, ignoring");
                return;
            }
            
            // Validate checksum
            uint8_t calc = calculateChecksum(msg);
            if (calc != msg.checksum) {
                Serial.println("RX: Checksum ERROR!");
                
                // Send NACK
                Message nack;
                nack.nodeID = MY_NODE_ID;
                nack.destID = msg.nodeID;
                nack.msgType = MSG_NACK;
                nack.msgCounter = msg.msgCounter;
                nack.payloadLen = 0;
                nack.checksum = calculateChecksum(nack);
                
                sendNACK(nack);
                return;
            }
            
            // Payload validation
            if (msg.payloadLen > 40) {
                Serial.println("RX: Payload too large!");
                return;
            }
            
            // Process message (application logic)
            Serial.println("RX: Valid message from " + String(msg.nodeID));
            displayMessage((char*)msg.payload, msg.payloadLen);
            
            // Send ACK
            Message ack;
            ack.nodeID = MY_NODE_ID;
            ack.destID = msg.nodeID;
            ack.msgType = MSG_ACK;
            ack.msgCounter = msg.msgCounter;  // Echo their counter
            strcpy((char*)ack.payload, "RECEIVED");
            ack.payloadLen = 8;
            ack.checksum = calculateChecksum(ack);
            
            sendACK(ack);
        }
    }
}

void sendACK(Message &ack) {
    uint8_t buf[sizeof(ack)] = {0};
    memcpy(buf, (uint8_t*)&ack, sizeof(ack));
    
    rf95.send(buf, sizeof(ack));
    rf95.waitPacketSent();
    
    Serial.println("TX: ACK sent");
}
```

**Breakdown:**
- Check if data available (non-blocking).
- Validate: size check, destination filter, checksum.
- If checksum bad, send NACK (negative ACK).
- If valid, process and send ACK echoing their message counter (so they know which message was received).
- Multi-layered validation: prevent buffer overruns, process invalid data safely.

---

## Summary: Key Patterns

| Pattern | Purpose |
|---------|---------|
| **BLE IRQ events** | Async notification of connection, discovery, read/write events |
| **REST routing** | Dispatch HTTP method+path to handler, return status codes |
| **HTTP request parsing** | Split headers from body, extract method/path, parse content |
| **MQTT QoS1 + retain** | At-least-once delivery; broker remembers latest state value |
| **MQTT LWT** | Auto-publish offline signal if connection drops |
| **Mesh dedupe cache** | Prevent infinite forwarding loops via origin:msgid tracking |
| **Mesh TTL** | Hop limit counter; decrement and drop if TTL reaches 0 |
| **LoRa checksum** | XOR all bytes; receiver validates to detect corruption |
| **LoRa ARQ** | Send + wait for ACK; retry with backoff if timeout |
| **Debounce** | Track state change with time threshold to ignore noise |

