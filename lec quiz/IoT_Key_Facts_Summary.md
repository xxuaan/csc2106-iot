# IoT Lecture Quiz — Key Facts Summary

**Exam:** 2 April 2026 · 9:15–10:00 AM · 50 Questions · Closed Book
**~60% of questions from weekly quizzes** — master these and you've covered the majority!

---

## Week 1: IoT Fundamentals

- **IoT ultimate aim** → To transform raw data into insights (not just connecting things)
- **IoT refers to** → All of them (interconnections, data exchange, ecosystem accessible via internet)
- **Middleware** → Allows heterogeneous systems to communicate
- **Longest range** → LoRa
- **IoT devices support** → All of: Sensing, Actuating, Data collection
- **NOT a component** → Power Supply is NOT an IoT device component (Controller, Sensor, Communication, Actuator ARE)
- **2.4GHz technologies** → Bluetooth/BLE, ZigBee, Wireless HART, WiFi, LoRa ✓  (NOT Z-Wave, Weightless, SigFox, NB-IoT)
- **IoT factors equally important?** → **False** (range, power, data rate, scalability, security and cost do NOT all have the same importance)

---

## Week 2: RESTful APIs

- **REST** = Representational State Transfer
- **Architecture** → Client-server
- **Resource** → Entity accessed/manipulated via a URI
- **Idempotence** → Same result no matter how many times request is made
- **State indicator** → HTTP status codes
- **NOT a constraint** → Cookie-based authentication (statelessness IS a constraint)
- **HTTP Methods:**
  - GET → Retrieve
  - POST → Create
  - PUT → Update
  - DELETE → Delete
- **200 OK** → Successful GET
- **5xx** → Server error
- **Uniform Interface** → Standardizes how clients interact with server
- **POST payload** → Contains data being sent by the client

---

## Week 3: CoAP & MQTT

### CoAP
- **Full name** = Constrained Application Protocol
- **Layer** = Application layer (runs over **UDP**, not TCP)
- **Modeled after** → HTTP
- **Specialized in** → Internet applications
- **Sub-layers** → Message layer + Request/Response layer
- **Supported methods** → GET, POST, PUT, DELETE (same as HTTP)
- **Message types** → Confirmable and Non-Confirmable
- **RFC** → RFC 7252
- **Provides** → Multicast support + lower overhead + simplicity

### MQTT
- **Layer** → Application protocol
- **Model** → Publish/Subscribe (message-oriented)
- **Publisher knows subscribers?** → The publisher DOESN'T KNOW
- **Last Will Message** → Notifies clients of a failure in the publisher
- **NOT a client type** → Producer (Publisher, Subscriber, Broker ARE client types)
- **QoS 1** → At least once delivery
- **NOT a characteristic** → Request/response messaging model

---

## Week 4: Mesh Networking

- **Primary feature** → Decentralized architecture
- **Self-healing** → Network automatically reroutes if a node fails
- **Simple flooding disadvantage** → Generates large number of redundant messages
- **Reduced Flooding** → Divides network into subnets for routing
- **Adaptive Flooding** → Adjusts based on number of connected nodes
- **Routes established** → Nodes communicate to determine adjacency and routes
- **Security** → Authentication prevents malicious nodes
- **NOT a feature** → Centralized architecture
- **NOT a challenge** → Unlimited bandwidth
- **DSR** → Has multiple routes per destination → **True**
- **OLSR** → Uses triggered updates on topology change → **False** (uses proactive/periodic updates)
- **OLSR** → Uses **Multipoint Relays** to optimize transmission

---

## Week 5: LoRa & LoRaWAN

- **LoRa** = Long Range
- **LoRaWAN** = Long Range Wide Area Network
- **Modulation** → Chirp Spread Spectrum (CSS)
- **OSI Layers:** LoRa = **Physical** layer; LoRaWAN = **Datalink** layer
- **Singapore frequency** → **923 MHz**
- **Spreading Factor (SF) increase** → Increases range, **Decreases** data rate
- **SF affects** → Data rate and range
- **Main drawback** → Limited data rate
- **Main advantages** → Low power consumption + Long range
- **Device EUI** → Uniquely identifies device on network
- **LoRaWAN Classes:**
  - Class A → Basic, all devices, lowest power
  - **Class B** → Scheduled receive slots (beacon-synchronized), battery-powered actuators
  - **Class C** → Minimal latency (always listening)
- **Network server** → Processes and forwards data to application servers
- **Link Budget** → Amount of loss a data link can TOLERATE to operate properly

---

## Week 6: WiFi & HaLow (802.11)

- **HaLow** = **802.11ah**
- **HaLow frequency** → **Sub-1 GHz**
- **HaLow range** → **Longer** than traditional Wi-Fi
- **HaLow advantages** → Greater range + Lower power consumption
- **All 802.11 devices compatible?** → **False**
- **2.4GHz overlapping channels** → **11**
- **Channel access** → CSMA with **Collision Avoidance**
- **Power management modes** → Power Save Mode + Active Mode
- **Beacon notification** → **Traffic Indication Map (TIM)**
- **Common IEEE 802 standards** → 802.1 Bridging + 802.2 Logical Link Control
- **Hidden node solution** → **CTS (Clear To Send) + RTS (Request To Send)** packets
- **Data Unit size** → Gets larger going **DOWN** the OSI stack
- **Coding scheme decision** → **Signal to Noise Ratio**
- **Relays in HaLow** → Lower data rate (low power) + Improve reliability
- **Duration/ID field** → **Response Indication Deferral** mechanism
- **Active scanning** → AP sends **Probe Response Frame**
- **Passive scanning** → Node sends **Association Request Frame**
- **NDP frames in 802.11ah** → RTS, ACK, CTS

---

## Week 7: Bluetooth & BLE

- **BLE** = Bluetooth Low Energy
- **Introduced in** → **Bluetooth 4.0**
- **Frequency** → **2.4 GHz**
- **Range** → Up to **100 meters**
- **Max data rate** → **2 Mbps**
- **Distinguishes from Classic BT** → Lower energy consumption
- **Scheme** → **Frequency hopping TDD**
- **Topologies (BLE)** → Point-to-Point, Star, Mesh
- **Topologies (BT 4.0)** → **Scatter Net + Star**
- **Pairing** → Exchanging security keys for secure communication
- **Advertising mode** → Device discovery and connection establishment
- **Advertising channels** → **3** (out of 40 total 2-MHz channels)
- **GATT** = **Generic Attribute Profile**
- **Central device** → Connects to and **controls** peripheral devices
- **Calculations:**
  - 300 bits/slot → 600 bits = **3 slots**
  - 300 bits/slot → 1200 bits = **5 slots**
  - Carrier freq = Total BW ÷ Signal BW = 180÷15 = **12**
  - PRN bits for 25 carriers = log₂(25) ≈ **5 bits**

---

## Week 8: Cloud, Edge & AIoT

- **Cloud Computing** → Access software and services over the internet
- **Cloud benefit** → Lower maintenance costs
- **Service Models:**
  - **IaaS** = Infrastructure as a Service
  - **PaaS** = Platform as a Service
  - **SaaS** = Software as a Service
  - **NOT** a model → Virtualization as a Service
- **Hypervisors:**
  - **Type 1** = Bare metal (does NOT host OS)
  - **Type 2** = Uses host OS (**NOT** bare metal)
- **Fog Computing** → Cloud = centralized; Fog = decentralized
- **NOT a Cloud advantage** (per lecture) → **Integration**
- **NOT an Edge advantage** → **Low investment cost**
- **NOT an Edge deployment challenge** → **Sound proofing**
- **AIoT** = **Artificial Intelligence on IoT**
- **IIoT** = Industrial IoT → **True**
- **AIoT purpose** → Integrate AI with IoT
- **NOT a benefit of AIoT** → Decreased complexity (AIoT **increases** complexity)
- **NOT a use case for AIoT industrial automation** → Employee time tracking

---

## Week 9: NB-IoT & Cellular IoT

- **NB-IoT modes** → Guard band, Stand Alone, In-Band (NOT "Out-Band")
- **Best use case** → Smart metering in rural areas
- **Standards body** → **3GPP**
- **Key advantage** → **Low Power Consumption**
- **HTTP problem for IoT** → High overhead and power consumption
- **PSM** → Power Saving Mode (devices "sleep" for extended periods)
- **NIDD advantage** → Eliminates IP overhead (optimized for small data packets)
- **SCEF** → Interfaces NIDD with IoT application servers
- **Bit rate determinants** → **Modulation scheme + Symbol rate**
- **Orthogonal CDMA codes** → `<0,1,0,1>` and `<1,0,1,0>` (dot product = 0)

---

## Week 10: Thread, 802.15.4 & 6LoWPAN

- **IEEE 802.15.4** → Low-rate wireless personal area networks
- **802.15.4 modulation** → **DSSS** (Direct Sequence Spread Spectrum)
- **802.15.4 layers** → **Physical (PHY) + Data Link Layer** only
- **802.15.4 basis for** → Thread **AND** ZigBee
- **6LoWPAN sits on top of** → **IEEE 802.15.4**
- **6LoWPAN full form** → **IPv6** over Low-Power **Wireless** Personal Area Networks
- **6LoWPAN = 802.15.4?** → **False**
- **6LoWPAN uses IPv4?** → **False** (uses **IPv6**)
- **Thread Border Router limit** → No hard limit of 3 ("max 3" is **FALSE**)
- **NOT a Thread characteristic** → "Devices can easily join the network" (joining is controlled/commissioned)
- **Thread TRUE** → Can employ **more than 10 routers**
- **Re-attach phases** → **Attaching** only (not Discovery/Commissioning/Configuring)
- **802.15.4 uses** → DSSS + **CSMA with Collision Avoidance**
- **EUI64 → Link Local conversion** → XOR bit 6 of first byte (universal/local bit flip)
  - EUI64 62::1 → 22::1 → **FE80:0:0:0:22::1**
- **DSSS calculation** → Transmitted bits ÷ Chipping sequence length = actual data bits
  - 330 ÷ 12 = **27.5 → 30 data bits** (330 bits / 12-chip sequence)

---

## Week 11: ZigBee & Z-Wave

- **Z-Wave = ZigBee?** → **False** (completely different protocols)
- **Z-Wave frequency** → **868 MHz** (NOT 2.4GHz, unlike ZigBee)
- **ZigBee purpose** → Healthcare + Industrial Control + Home Automation (**All of the above**)
- **ZigBee Coordinator** → **Both** controls AND communicates
- **ZigBee End Device** → Only **communicates** with other nodes
- **Routing** → End Device does **NOT** route (Coordinator and Router do)
- **NOT a ZigBee topology** → **Bus** (Star, Mesh, Cluster Tree ARE supported)
- **ZigBee Compliant Product needs** → IEEE 802.15.4 + Application Profile + ZigBee Stack
- **NOT a ZigBee feature** → **Large Bandwidth** (ZigBee is low bandwidth)

---

## Quick Reference: Key Acronyms

| Acronym | Full Form |
|---------|-----------|
| REST | Representational State Transfer |
| CoAP | Constrained Application Protocol |
| MQTT | Message Queuing Telemetry Transport |
| LoRa | Long Range |
| LoRaWAN | Long Range Wide Area Network |
| CSS | Chirp Spread Spectrum |
| BLE | Bluetooth Low Energy |
| GATT | Generic Attribute Profile |
| IaaS | Infrastructure as a Service |
| SaaS | Software as a Service |
| PaaS | Platform as a Service |
| AIoT | Artificial Intelligence on IoT |
| IIoT | Industrial Internet of Things |
| PSM | Power Saving Mode (NB-IoT) |
| NIDD | Non-IP Data Delivery |
| SCEF | Service Capability Exposure Function |
| 6LoWPAN | IPv6 over Low-Power Wireless Personal Area Networks |
| DSSS | Direct Sequence Spread Spectrum |
| OLSR | Optimized Link State Routing |
| DSR | Dynamic Source Routing |
| TIM | Traffic Indication Map |

---

*Good luck on 2 April! Please arrive by 9:00 AM at your assigned venue.*
