# BLE Pico W Lab MCQ Questions and Answers

## Section A: Core MCQs

1. In this lab, the board that advertises and hosts GATT data is the:
A. Observer
B. Broadcaster-only
C. Peripheral
D. Scanner

2. The board that scans and initiates connection is the:
A. Peripheral
B. Central
C. Beacon
D. GATT server only

3. The custom service UUID used is:
A. 0x1A00
B. 0x1A01
C. 0x1A02
D. 0x2902

4. The LED characteristic UUID is:
A. 0x1A00
B. 0x1A01
C. 0x1A02
D. 0x180A

5. The status characteristic supports:
A. WRITE only
B. READ and NOTIFY
C. NOTIFY only
D. INDICATE only

6. Why is CCCD needed?
A. To discover services
B. To encrypt packets
C. To configure notifications and indications
D. To set advertising interval

7. Value to enable notifications is:
A. 0x0001
B. 0x0100
C. 0x1000
D. 0xFFFF

8. A likely reason notify fails while read works is:
A. Client did not write CCCD
B. RSSI too strong
C. Service UUID too short
D. Pull-up resistor enabled

9. Button debounce is used to:
A. Increase BLE range
B. Prevent repeated false presses
C. Improve UUID matching
D. Force reconnect

10. Characteristic discovery should happen:
A. Before scan
B. After successful connect and service discovery
C. Only after disconnect
D. Only on server

11. Correct Task 4 path is:
A. Client write -> server write IRQ -> server LED update
B. Server notify -> client write
C. Client read -> server disconnect
D. CCCD write -> LED toggles

12. Notify is often better than polling because:
A. No stack needed
B. Server pushes updates only when needed
C. UUID is not required
D. It disables interrupts

13. On disconnect, server should:
A. Stop BLE forever
B. Re-advertise
C. Delete service
D. Reset board

14. BLE logic in this lab is mostly:
A. Event driven using IRQ callbacks
B. Blocking synchronous only
C. Interrupt disabled loops
D. File system driven

15. A practical recovery when discovery fails is:
A. Guess random handles
B. Disconnect and retry cleanly
C. Disable notifications permanently
D. Change ADC pin

16. Beacon mode differs because beacon:
A. Never advertises
B. Advertises only and does not run connectable GATT exchange
C. Requires Wi-Fi
D. Uses only write requests

17. CCCD UUID is:
A. 0x2A00
B. 0x2800
C. 0x2902
D. 0x180D

18. BLE handle usually refers to:
A. Pin number
B. ATT attribute index
C. Device name pointer
D. RSSI threshold

19. If target name filter mismatches, client likely:
A. Still connects to target
B. Fails to find intended server
C. Enables notify anyway
D. Skips scan stage

20. Why avoid hardcoded handles?
A. Handles are encrypted
B. Runtime layout can change handle values
C. Handles are Wi-Fi specific
D. Handles are random each packet

21. Read flow requires:
A. gattc_read plus read result/done events
B. CCCD write only
C. gap_advertise call
D. Service registration on client

22. WRITE_NO_RESPONSE is useful when:
A. Lowest overhead and low latency are preferred
B. Maximum acknowledgment is required
C. Read is disabled
D. Scan is disabled

23. Server notify targets:
A. All relevant active connection handles
B. Only first client forever
C. No clients unless scanning
D. Only disconnected clients

24. Robust write handling should:
A. Crash on parse errors
B. Validate and safely handle malformed values
C. Ignore all writes
D. Disable LED updates

25. LED state value format in this lab is generally:
A. float32 binary
B. UTF-8 bytes for 0 or 1
C. encrypted JSON only
D. UUID text

### Answers: Section A
1. C
2. B
3. C
4. A
5. B
6. C
7. B
8. A
9. B
10. B
11. A
12. B
13. B
14. A
15. B
16. B
17. C
18. B
19. B
20. B
21. A
22. A
23. A
24. B
25. B

## Section B: Harder Scenario MCQs

26. Client logs show service found and characteristics found, but no notify events appear. Most probable direct cause is:
A. Wrong advertising interval
B. CCCD was not written successfully
C. LED characteristic is read only
D. Client scan window too small

27. Client reads LED correctly but writes do nothing on server. Most likely issue is:
A. Wrong LED characteristic handle used for write path
B. Status string decode failed
C. Client button debounce too high
D. ADC formula is incorrect

28. Server disconnect event happens and client cannot reconnect unless server is reset. Most likely missing logic:
A. Re-register services
B. Re-enable ADC
C. Re-start advertising on disconnect
D. Rebuild UUID objects

29. Client found target name, connected, then disconnected after service discovery done with no target service found. Most likely explanation:
A. Service UUID mismatch between server and client
B. RSSI too low for read
C. Status characteristic not notifiable
D. LED write mode set to no response

30. Descriptor discovery returns no CCCD, and client uses status_handle plus 1 fallback. Primary risk is:
A. Battery drain only
B. Writing to wrong attribute if layout assumptions are false
C. Device name corruption
D. Duplicate advertising packets

31. User presses Button B once but server toggles LED multiple times. Most likely immediate fix:
A. Decrease MTU size
B. Add or improve button debounce logic
C. Disable notifications
D. Increase advertising payload size

32. Server crashes occasionally on incoming writes from unknown clients. Most robust coding improvement:
A. Add try/except and range validation on parsed LED value
B. Disable write support
C. Remove IRQ handler
D. Only allow reads

33. In a multi-client future extension, server stores one conn_handle only. Likely issue:
A. Only one client may receive notifications correctly
B. UUID conflict at boot
C. Service registration fails immediately
D. Client scan cannot stop

34. Client receives notify on unexpected handle and ignores data. Correct interpretation:
A. Bug in BLE hardware
B. Handler should verify the handle map from discovery and match correctly
C. Advertising packet malformed
D. ADC channel unavailable

35. Client starts scanning indefinitely and never connects even though server is present. Most diagnostic first step:
A. Verify target name in scan filter matches actual advertised name exactly
B. Change button pin numbers
C. Disable status characteristic
D. Force CCCD write before connect

36. Why might WRITE_NO_RESPONSE be selected for LED control in this lab?
A. It can reduce transaction overhead for simple state updates
B. It guarantees stronger delivery semantics than write with response
C. It enables notifications automatically
D. It bypasses service discovery

37. Client local LED toggles immediately on button write, but server LED remains unchanged due to failed write. Best UI improvement:
A. Update local LED only after write success callback
B. Remove local LED usage entirely
C. Toggle local LED twice
D. Increase sleep delay

38. Notify path works once, then not again after reconnect. Most likely design oversight:
A. CCCD subscription was not re-established on new connection
B. Service UUID became invalid
C. ADC conversion changed MTU
D. Scan interval too short

39. In event-driven BLE code, race conditions are reduced by:
A. Assuming event order never changes
B. Tracking connection and handle state explicitly and checking before actions
C. Removing all callbacks
D. Forcing fixed delays only

40. Which statement best captures read result versus read done semantics?
A. Both carry identical payload always
B. Read result carries value data; read done indicates operation completion status
C. Read done always arrives first
D. Read result is only for notify

### Answers: Section B
26. B
27. A
28. C
29. A
30. B
31. B
32. A
33. A
34. B
35. A
36. A
37. A
38. A
39. B
40. B
