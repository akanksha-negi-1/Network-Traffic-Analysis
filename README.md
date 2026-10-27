# Network Traffic Analysis

## Overview

This project demonstrates a practical network traffic analysis workflow using Wireshark. It captures and analyzes normal and suspicious network activity at the packet level, focusing on communication purpose, packet behavior, destination patterns, repetition, and response behavior.

The project contains seven controlled traffic scenarios:

- 4 normal traffic scenarios
- 3 suspicious traffic scenarios

## Objectives

- Capture network traffic using Wireshark.
- Analyze common network protocols at the packet level.
- Identify recognizable request/response patterns.
- Compare normal and suspicious traffic based on observable packet patterns.
- Document packet-level evidence and findings.

## Tools

- Wireshark
- Windows PowerShell
- Python HTTP server
- Local network devices used to generate controlled traffic

## Traffic Scenarios

### Normal Traffic

| # | Experiment | Traffic | Main Observation |
|---|---|---|---|
| 1 | DNS | DNS resolution | A and AAAA queries and successful responses |
| 2 | ICMP | Ping communication | ICMP Echo Request and Echo Reply pairs |
| 3 | HTTP | Web/resource retrieval | HTTP GET requests and server responses |
| 4 | HTTPS | Encrypted web communication | TLS 1.3 handshake and encrypted Application Data |

### Suspicious Traffic

| # | Experiment | Traffic | Main Observation |
|---|---|---|---|
| 5 | Repeated Failed TCP Connections | Repeated TCP connection attempts | Repeated SYN attempts and retransmissions against the same target port |
| 6 | Port Scanning | Multi-port TCP probing | SYN probes directed toward multiple destination ports |
| 7 | ICMP Burst | Repeated ICMP requests | Repeated Echo Requests toward the same target |

## Methodology

Each experiment followed a controlled capture-and-analysis workflow:

1. Generate the intended network activity.
2. Capture the traffic in Wireshark.
3. Apply an appropriate display filter.
4. Identify relevant packets and packet-level behavior.
5. Save the packet capture as a `.pcapng` file.
6. Document the observations and findings in a Markdown analysis file.

The suspicious scenarios were generated in a controlled environment for analysis purposes.

## Key Analysis Patterns

The project demonstrates that traffic classification should not rely only on the protocol being used. The way a protocol is used is also important.

### Normal Traffic

```text
DNS      → Query → Response
ICMP     → Echo Request → Echo Reply
HTTP     → GET → HTTP Response
HTTPS    → TLS Handshake → Encrypted Application Data
```

### Suspicious Traffic

```text
Repeated Failed TCP Connections
→ Repeated SYN attempts → Retransmissions

Port Scanning
→ SYN probes → Multiple destination ports

ICMP Burst
→ Repeated Echo Requests → Same target
```

These patterns are indicators for further investigation and do not automatically prove malicious activity.

## Key Findings

### Repetition

Repeated activity can be significant when it does not match the expected communication pattern.

Examples observed in the project include:

- Repeated TCP SYN attempts
- Repeated SYN probes across different ports
- Repeated ICMP Echo Requests

### Destination Behavior

Destination behavior helped distinguish the suspicious scenarios:

- **Repeated Failed TCP Connections:** same target and same destination port.
- **Port Scanning:** same target with multiple destination ports.
- **ICMP Burst:** same target with repeated ICMP requests.

### Response Behavior

The normal scenarios commonly showed expected communication responses:

- DNS Query → DNS Response
- ICMP Echo Request → Echo Reply
- HTTP GET → HTTP Response
- TLS handshake → encrypted Application Data

The suspicious scenarios showed incomplete or unusual response behavior, including TCP SYN retransmissions and ICMP requests for which no corresponding replies were visible in the displayed traffic.

## What This Project Demonstrates

The seven experiments demonstrate a basic network-traffic analysis workflow in which:

1. Normal protocol behavior is identified.
2. Unusual traffic patterns are isolated.
3. Packet-level evidence is examined.
4. Repetition, targeting behavior, and response behavior are compared.
5. Suspicious patterns are documented.

A key takeaway is that the same protocol can represent normal or suspicious activity depending on how it is used, how frequently it is sent, where it is directed, and whether the expected communication pattern occurs.

## Evidence

Each experiment contains:

- A `.pcapng` packet capture
- An analysis documenting the observations and findings

The project-level comparison is documented in:

[`Normal-vs-Suspicious-Network-Traffic-Analysis.md`](Normal-vs-Suspicious-Network-Traffic-Analysis.md)

## Conclusion

This project provides hands-on practice in analyzing network traffic with Wireshark and recognizing patterns associated with both normal communication and suspicious activity.

The analysis emphasizes packet-level evidence rather than assumptions. The resulting observations provide a foundation for identifying traffic that may require further investigation in a security monitoring or network-analysis workflow.
