# REST Pico W Lab Study Notes

## 1. Lab Overview
This lab builds a simple REST-style HTTP API on Raspberry Pi Pico W using MicroPython.
- Core files:
  - `main.py`: Wi-Fi connect, socket setup, server loop, sensor read, LED apply
  - `web_server.py`: HTTP parsing, route handling, response generation
- Main idea: expose sensor data and control actuator over HTTP endpoints.

## Explain It Fast: What / Does / Why

### REST Endpoint
- What is this: URL + HTTP method pair representing an API action.
- What it does: maps request intent (read temp, set LED, read status) to handler logic.
- Why it is done: predictable machine-to-machine contract and testable behavior.

### HTTP Method Semantics
- What is this: operation meaning carried by GET/POST.
- What it does: GET reads state; POST changes state.
- Why it is done: enforces API clarity and helps avoid accidental state changes on reads.

### Status Codes
- What is this: protocol-level result signal.
- What it does: tells client success, bad input, wrong method, or unknown route.
- Why it is done: clients can branch logic automatically instead of parsing human text.

### JSON Response Contract
- What is this: structured response body for `/status`.
- What it does: exposes `temperature` and `led_on` in machine-readable format.
- Why it is done: dashboards/scripts can parse data reliably.

### Route Parser
- What is this: request-line/header/body extraction from raw socket bytes.
- What it does: turns raw HTTP text into method/path/body values used by route logic.
- Why it is done: routing and validation depend on correct parse.

### Main Loop Hardware Authority
- What is this: architectural split where `main.py` owns GPIO/sensor operations.
- What it does: applies LED change returned by handler sentinel (`-1`, `0`, `1`).
- Why it is done: keeps networking logic separate from hardware side effects.

## Beginner Deep Dive (If You Are New)

### Mental model
Think of REST API as a menu:
- URL path is the menu item (`/temp`, `/led`, `/status`).
- HTTP method is the action type (read vs change).
- Status code tells client whether request succeeded.

### One full request lifecycle
1. Client sends HTTP request to Pico IP + route.
2. Server parses method, path, headers, and body.
3. Router chooses matching handler branch.
4. Handler validates input and builds response.
5. Main loop applies hardware change if command was returned.
6. Connection closes after response.

### Why this structure is used
- Method+path contract makes API behavior predictable.
- Validation protects device from malformed input.
- JSON on `/status` allows scripts/apps to parse data safely.
- Main loop ownership of GPIO avoids hidden side effects in parser code.

### Beginner mistakes and how to avoid them
- Mistake: returning 200 for invalid body.
  Fix: return 400 for malformed payload.
- Mistake: using wrong method on valid route.
  Fix: return 405 for method mismatch.
- Mistake: unknown route mapped to root page.
  Fix: return 404 for unknown path.
- Mistake: forgetting `Content-Type: application/json` on `/status`.
  Fix: set explicit JSON content type and serialize dict via `json.dumps`.

### Why status codes are important
Clients should not guess from human text.
- 200: request accepted/success.
- 400: client input format/value is invalid.
- 404: endpoint does not exist.
- 405: endpoint exists but method is not allowed.

### Beginner Flow Diagram (Text)
```text
CLIENT (Browser/Postman)                  PICO W SERVER
Send HTTP request -----------------------> Accept socket connection
  (method + path + body)                   |
                                           Parse request line/headers/body
                                           |
                                           Route by method+path
                                           |-- GET /temp   -> build temp response
                                           |-- POST /led   -> validate body, return LED command
                                           |-- GET /status -> return JSON state
                                           |-- otherwise   -> 404 or 405
                                           |
Receive HTTP response <------------------ Send status line + headers + body
                                           |
                                       Main loop applies LED state if commanded
```

## 2. Learning Outcomes
- Connect Pico W to Wi-Fi in STA mode.
- Run an HTTP server on port 80.
- Parse HTTP request method, path, headers, body.
- Implement route-based API behavior.
- Return suitable status codes and content types.
- Control onboard LED from POST request body.
- Return machine-readable JSON status for external clients.

## 3. System Architecture
1. Pico boots and connects to Wi-Fi.
2. Socket binds to `0.0.0.0:80` and listens.
3. For each client:
   - Read current internal temperature.
   - Read current LED state.
   - Pass request to `serve_client(...)`.
   - Apply returned LED command if present.

## 4. Endpoints in This Lab
- `GET /`
  - Returns API root page and endpoint summary.
- `GET /temp`
  - Returns current temperature.
- `POST /led`
  - Accepts LED state from request body and updates LED.
- `GET /status` (assignment extension)
  - Returns JSON containing temperature and current LED state.

## 5. Request Parsing Essentials
Typical parser extracts:
- HTTP method (GET, POST)
- Path (`/temp`, `/led`, `/status`)
- Headers (e.g., `Content-Type`)
- Body payload

Assumptions in this simple server:
- Small request payloads fit in initial `recv`.
- Basic parsing only, no full HTTP edge-case handling.

## 6. Response Essentials
A valid response includes:
- Status line: `HTTP/1.1 <status> <reason>`
- Headers: at least `Content-Type`, `Connection: close`
- Blank line
- Response body

Common statuses used:
- 200 OK
- 400 Bad Request
- 404 Not Found
- 405 Method Not Allowed

## 7. POST /led Body Formats
The server accepts LED state in multiple formats:
- JSON: `{ "state": 1 }`
- Form: `state=1`
- Plain text: `1`

Rules:
- Valid state is `0` or `1`.
- Invalid payload should return 400 Bad Request.

## 8. /status Assignment Requirement
`GET /status` must return JSON and include:
- `temperature` (numeric)
- `led_on` (boolean)

Expected Content-Type:
- `application/json`

Example:
```json
{
  "temperature": 24.55,
  "led_on": true
}
```

## 9. Main Loop State Flow
- `main.py` reads hardware state each request cycle.
- Passes current LED value into web handler.
- Handler returns LED command sentinel:
  - `-1`: no change
  - `0`: set OFF
  - `1`: set ON
- `main.py` is final authority that applies LED hardware update.

## 10. Common Mistakes
- Forgetting to set `Content-Type: application/json` for `/status`.
- Returning HTML instead of JSON for machine endpoint.
- Not passing LED current state into handler.
- Updating response body but not status code on errors.
- Accepting malformed body without validation.
- Not handling unsupported method with 405.
- Missing fallback route for unknown paths (404).

## 11. Testing Checklist (Postman)
1. `POST /led` with `{ "state": 1 }` -> LED turns ON.
2. `GET /status` -> `200`, JSON valid, `led_on: true`.
3. `POST /led` with `{ "state": 0 }` -> LED turns OFF.
4. `GET /status` -> `led_on: false`.
5. `GET /temp` -> numeric temperature shown.
6. Invalid `POST /led` body -> `400 Bad Request`.
7. Wrong method on route -> `405 Method Not Allowed`.
8. Unknown path -> `404 Not Found`.

## 12. Viva One-Liners
- REST endpoint behavior is determined by method + path.
- JSON endpoints are for machine-to-machine readability.
- 405 means path exists but method is wrong.
- 404 means path is unknown.
- Input validation prevents malformed requests from crashing logic.
- Separate networking/router logic from hardware-control loop for clarity.

## Code Focus

### Actual code files to master
- assignment/main.py
- assignment/web_server.py

### Main loop checkpoints
- Understand Wi-Fi connect state checks and failure handling.
- Trace accept -> read temp -> read LED state -> call serve_client -> apply returned LED command.
- Explain why hardware actuation is centralized in main loop.

### Web handler checkpoints
- Trace request parsing into method, path, headers, and body.
- Explain route behavior for root, temp, led, and status paths.
- Explain when to return 200, 400, 404, and 405.
- Show exactly how content type changes between HTML and JSON responses.

### Code-level test prompts
- Why does status endpoint need current LED value passed from main?
- Which parser branch handles JSON, form, and plain text LED body?
- Where should malformed payload exceptions be trapped to avoid server crash?

## In-Depth Code Walkthrough

### 1) Main loop as orchestrator
```python
client_conn, client_addr = s.accept()
current_temp_c = read_temperature()
current_led_value = onboard_led.value()
new_led_state = serve_client(client_conn, current_temp_c, current_led_value)

if new_led_state == 0:
  onboard_led.value(0)
elif new_led_state == 1:
  onboard_led.value(1)
```
Why this matters:
- `main.py` owns hardware reads/writes and keeps request handling deterministic.
- `serve_client` decides intent; `main.py` applies actuator change.

### 2) HTTP parse skeleton
```python
parts = raw.split("\r\n\r\n", 1)
header_block = parts[0]
body = parts[1] if len(parts) > 1 else ""

request_line = header_lines[0]
method, path, _ = request_line.split(" ", 2)
```
Why this matters:
- Shows how method/path/body are extracted from raw socket bytes.
- Also reveals parser limitation: assumes payload is fully available in one recv.

### 3) LED payload parsing by content type
```python
if ct == "application/json":
  data = json.loads(body)
  state = data.get("state", None)
elif ct == "application/x-www-form-urlencoded":
  # parse state=0 or state=1
else:
  txt = body.strip()
```
Why this matters:
- You can explain multi-format input handling in short-answer questions.
- Input validation drives 400 Bad Request branch.

### 4) Status route JSON contract
```python
status_obj = {
  "temperature": temp_c,
  "led_on": bool(led_is_active)
}
body = json.dumps(status_obj)
response = http_response(body, status=200, content_type="application/json")
```
Why this matters:
- This is the assignment requirement in code form: JSON body and JSON MIME type.

### 5) Route-method correctness pattern
```python
if path == "/temp":
  if method != "GET":
    response = http_response(..., status=405)
elif path == "/led":
  if method != "POST":
    response = http_response(..., status=405)
else:
  response = http_response(..., status=404)
```
Why this matters:
- Clear exam distinction:
  - `405`: route exists, wrong method.
  - `404`: route does not exist.
