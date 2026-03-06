# Analysis Notes

## Summary
This document records the network traffic observations from the **Reverse Shell Network Detection Study** lab. The purpose of the analysis was to compare normal baseline traffic against suspicious outbound TCP activity and identify patterns that may indicate post-compromise communication.

The review focused on two suspicious scenarios:
- an interactive outbound TCP session on port `4444`
- repeated beacon-like outbound connections on port `5555`

All packet analysis was performed in Wireshark.

## Lab Hosts
- **Ubuntu Victim:** `192.168.28.128`
- **Kali Analyzer / Listener:** `192.168.28.129`

## Capture Files Reviewed
- `01-baseline.pcapng`
- `02-interactive-session-4444.pcapng`
- `03-beacon-5555.pcapng`

## 1. Baseline Traffic Analysis

### Purpose
The baseline capture was used to observe normal communication between the two lab systems before suspicious traffic was generated.

### Observed Activity
The baseline traffic contained:
- ICMP echo requests and replies
- HTTP traffic to a local Python web server on port `8000`

### Findings
- Traffic was limited to expected lab communication.
- No traffic to port `4444` was observed.
- No traffic to port `5555` was observed.
- No repeated callback pattern or interactive plaintext session was present.

### Interpretation
This capture established the normal network profile of the lab and served as a reference point for identifying unusual behavior in later captures.

## 2. Interactive TCP Session Analysis (Port 4444)

### Purpose
This scenario was designed to simulate suspicious outbound communication that resembles an interactive reverse-shell-style session.

### Filter Used
```text
tcp.port == 4444
```

### Observed Activity
The Ubuntu victim (`192.168.28.128`) initiated an outbound TCP connection to Kali (`192.168.28.129`) on port `4444`.

The capture showed:
- a successful TCP handshake
- bidirectional data exchange
- readable plaintext traffic in the TCP stream
- a sustained session rather than a single request

### Findings
- The victim initiated the connection to a non-standard destination port.
- The traffic pattern was interactive and two-way.
- Plaintext content was visible in the stream.
- This behavior was not present in the baseline capture.

### Interpretation
This traffic is suspicious because it differs from normal baseline behavior in both port usage and communication pattern. An outbound connection to an uncommon port combined with interactive plaintext traffic would be a strong indicator for further investigation in a real environment.

## 3. Beacon-Like Activity Analysis (Port 5555)

### Purpose
This scenario was created to simulate repeated short outbound connections resembling callback or beaconing behavior.

### Filters Used
```text
tcp.port == 5555
```

```text
tcp.dstport == 5555 && tcp.flags.syn == 1 && tcp.flags.ack == 0
```

### Observed Activity
The Ubuntu victim repeatedly initiated outbound TCP connections to Kali on port `5555`.

The traffic pattern showed:
- multiple short-lived sessions
- repeated connections to the same IP and port
- small amounts of transmitted data
- a regular timing pattern between connections

### Findings
- Traffic was repetitive and low-volume.
- The same destination IP and destination port were used across multiple sessions.
- The pattern was clearly different from normal baseline traffic.
- The repeated timing made the activity more suspicious.

### Interpretation
This activity resembles beacon-like behavior because it involves repeated outbound communication to the same destination at regular intervals. In a real monitoring environment, this type of traffic would be worth investigating as a possible callback or persistence-related pattern.

## 4. Comparison Against Baseline

### Port 4444
Compared to the baseline capture, the traffic on port `4444` introduced:
- a previously unseen destination port
- a sustained outbound session
- interactive two-way traffic
- visible plaintext content

### Port 5555
Compared to the baseline capture, the traffic on port `5555` introduced:
- repeated short outbound sessions
- consistent use of the same destination IP and port
- a periodic timing pattern
- behavior not associated with normal lab traffic

### Overall Difference
Both suspicious scenarios were easily distinguishable from baseline activity because they introduced:
- uncommon outbound ports
- unusual connection behavior
- traffic patterns not observed during normal communication

## 5. Detection Opportunities
This analysis highlights several useful network detection opportunities:

- monitor outbound connections to uncommon ports
- baseline expected host communication patterns
- investigate interactive plaintext TCP sessions
- detect repeated short connections to the same destination
- use conversation statistics and traffic timing to support alert triage

## 6. Conclusion
The packet captures in this project demonstrate how suspicious outbound communication can be identified through packet analysis. By comparing normal baseline traffic with interactive and beacon-like sessions, it becomes easier to recognize indicators of potentially malicious activity.
