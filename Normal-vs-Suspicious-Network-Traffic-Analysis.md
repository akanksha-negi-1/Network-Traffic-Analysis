# Normal vs Suspicious Network Traffic Analysis

## Purpose

This document compares the seven traffic scenarios captured in the project and highlights how normal network traffic can be differentiated from suspicious traffic using packet-level characteristics in Wireshark.

The distinction is based on the traffic patterns observed in the captures, including communication purpose, packet behavior, destination patterns, repetition, and connection behavior.

## 1. Normal vs Suspicious Traffic

Normal network traffic generally has a clear communication purpose and a predictable request/response pattern. Examples in this project include DNS resolution, ICMP communication, HTTP resource retrieval, and HTTPS communication.

Suspicious traffic can stand out because of unusual repetition, repeated unsuccessful connection attempts, or probing of multiple ports. In this project, the suspicious scenarios were repeated failed TCP connections, port scanning, and ICMP Burst.

These patterns do not automatically prove malicious activity. However, they are useful indicators that can justify further investigation.

## 2. Comparison of All Seven Traffic Scenarios

| Experiment | Traffic Type | Main Pattern | Destination Behavior | Response Pattern | Key Indicator |
|---|---|---|---|---|---|
| DNS | Normal | Domain name resolution | DNS server, port 53 | Query → response | Expected request/response activity |
| ICMP | Normal | Ping communication | One destination | Echo Request → Echo Reply | Normal two-way ICMP communication |
| HTTP | Normal | Web/resource requests | Specific server, port 8080 | GET → HTTP response | Clear client-server exchange |
| HTTPS | Normal | Encrypted web communication | Specific server | TLS handshake → encrypted data | TLS handshake and Application Data |
| Repeated Failed TCP Connections | Suspicious | Repeated SYN attempts | Same target port 9999 | No successful connection visible | Repeated unsuccessful attempts |
| Port Scanning | Suspicious | SYN probes across many ports | Multiple destination ports | Retransmissions/no expected responses | Rapid change in destination ports |
| ICMP Burst | Suspicious | Repeated ICMP Echo Requests | Same target | No corresponding replies visible | High repetition toward one target |

## 3. Detailed Differences

### 3.1 DNS — Normal

**Observed pattern:**

`DNS Query → DNS Response`

The capture showed queries for `linkedin.com` followed by successful A and AAAA responses.

**Why it appears normal:**

- The traffic had a clear purpose: resolving a domain name.
- Queries were directed to a DNS server.
- Responses returned the requested DNS information.
- The communication followed the expected query/response pattern.

**Useful indicator:**

A normal DNS exchange usually contains a recognizable request followed by a corresponding response.

### 3.2 ICMP — Normal

**Observed pattern:**

`Echo Request → Echo Reply`

The capture contained four complete ICMP Echo Request/Reply pairs between `192.168.1.8` and `8.8.8.8`.

**Why it appears normal:**

- The traffic involved a small number of ping requests.
- Each request had a corresponding response.
- Communication was directed toward a single destination.
- The pattern was consistent with connectivity testing.

**Useful indicator:**

A small number of ICMP requests with corresponding replies is generally more consistent with normal connectivity testing than a large repeated burst.

### 3.3 HTTP — Normal

**Observed pattern:**

`HTTP GET → HTTP Response`

The client (`192.168.1.8`) requested resources from the server (`192.168.1.9`) on port `8080`.

The capture included successful `200 OK` responses and one `404 File not found` response for `/favicon.ico`.

**Why it appears normal:**

- The client and server had defined roles.
- Requests were directed to one known server.
- The traffic consisted of recognizable HTTP requests and responses.
- The `404` response was a normal application-level response to a resource that was not present.

**Useful indicator:**

Normal HTTP traffic commonly shows a recognizable sequence of client requests followed by server responses.

### 3.4 HTTPS — Normal

**Observed pattern:**

`Client Hello → Server Hello → Encrypted Application Data`

The capture showed TLS 1.3 communication between `192.168.1.8` and `151.101.0.223`, with `python.org` identified through SNI.

**Why it appears normal:**

- The traffic showed a recognizable TLS handshake.
- The communication involved a specific server.
- Application Data followed the handshake.
- The application content was encrypted, which is expected for HTTPS.

**Useful indicator:**

The presence of a normal TLS handshake followed by encrypted Application Data is expected for HTTPS communication.

### 3.5 Repeated Failed TCP Connections — Suspicious

**Observed pattern:**

`SYN → retransmission → repeated new SYN attempts`

The capture showed repeated connection attempts from `192.168.1.9` to `192.168.1.8` on port `9999`.

Different source ports were used for the attempts, while the destination port remained the same.

**Why it appears suspicious:**

- The same destination port was repeatedly targeted.
- Multiple connection attempts were made.
- SYN retransmissions were visible.
- No successful TCP connection establishment was visible in the filtered traffic.

**Useful indicator:**

Repeated unsuccessful connection attempts against the same destination can be a sign of probing or repeated connection activity requiring investigation.

### 3.6 Port Scanning — Suspicious

**Observed pattern:**

`SYN → port 1 → SYN → port 2 → SYN → port 3 → ...`

The capture showed SYN probes from `192.168.1.9` to `192.168.1.8` across multiple destination ports.

**Why it appears suspicious:**

- The destination port changed repeatedly.
- Multiple ports on the same target were probed.
- SYN retransmissions were visible.
- The traffic formed a recognizable multi-port probing pattern.

**Useful indicator:**

A host contacting many different destination ports on the same target in a short period is a strong network-level indicator of port-scanning behavior.

### 3.7 ICMP Burst — Suspicious

**Observed pattern:**

`Echo Request → Echo Request → Echo Request → ...`

The capture showed repeated ICMP Echo Requests from `192.168.1.9` to `192.168.1.8`.

The requests had increasing sequence numbers, showing that multiple successive requests were being sent.

**Why it appears suspicious:**

- Many ICMP requests were directed toward the same target.
- The requests appeared repeatedly in the capture.
- The displayed requests showed `(no response found!)`.
- The traffic formed a recognizable burst pattern.

**Useful indicator:**

A high volume of repeated ICMP requests toward one target can be an indicator of abnormal or potentially abusive ICMP activity and should be investigated in context.

## 4. Main Differences Between Normal and Suspicious Traffic

### A. Communication Purpose

**Normal traffic:**
- DNS resolves a domain.
- ICMP tests connectivity.
- HTTP retrieves resources.
- HTTPS provides encrypted application communication.

**Suspicious traffic:**
- Repeated failed TCP connections repeatedly target a service.
- Port scanning probes multiple ports.
- ICMP burst generates repeated requests toward one target.

The purpose may not always be obvious from packets alone, so the observed traffic pattern is important.

### B. Repetition

Normal traffic can contain repeated packets, but the repetition usually supports a recognizable communication process.

Suspicious traffic can show unusually repetitive behavior, such as:

- Repeated SYN attempts
- Repeated probes to different ports
- Repeated ICMP Echo Requests

### C. Destination Pattern

This is especially useful for distinguishing the suspicious experiments:

- **Repeated Failed TCP Connections:** same target and same destination port.
- **Port Scanning:** same target but many destination ports.
- **ICMP Burst:** same target with repeated ICMP requests.

### D. Response Behavior

Normal traffic in this project commonly showed expected responses:

- DNS Query → DNS Response
- ICMP Echo Request → Echo Reply
- HTTP GET → HTTP response
- TLS handshake → encrypted Application Data

The suspicious captures showed incomplete or unusual response behavior:

- Repeated TCP SYN attempts and retransmissions
- Port probes with retransmissions
- Large number of ICMP requests with no corresponding replies visible in the displayed traffic

## 5. Practical Wireshark Indicators

When analyzing an unknown capture, the following patterns can help identify traffic that deserves further investigation:

| Observation | Possible Interpretation |
|---|---|
| Normal request followed by expected response | Likely normal communication |
| Repeated requests to the same service | Repeated connection/probing activity |
| SYN packets targeting many ports | Possible port scanning |
| SYN retransmissions without successful connection | Failed connection attempts or probing |
| Large number of ICMP Echo Requests | Possible ICMP burst/flood-like activity |
| Recognizable TLS handshake followed by Application Data | Expected HTTPS behavior |
| One client communicating with a known server using expected application protocols | More consistent with normal traffic |

These indicators should be considered in context. A single unusual packet does not establish malicious activity.

## 6. Overall Project Findings

The seven experiments demonstrate that network traffic can often be differentiated by looking at **patterns rather than individual packets**.

The four normal scenarios showed recognizable communication processes:

1. **DNS** — domain resolution
2. **ICMP** — connectivity testing
3. **HTTP** — client-server resource retrieval
4. **HTTPS** — encrypted client-server communication

The three suspicious scenarios showed patterns that stood out from those normal communication flows:

5. **Repeated Failed TCP Connections** — repeated attempts against the same destination port
6. **Port Scanning** — probing multiple destination ports
7. **ICMP Burst** — repeated ICMP requests toward the same target

The most important distinction is therefore not simply the protocol being used. **The same protocol can be normal or suspicious depending on how it is used, how frequently it is sent, where it is directed, and whether the expected communication pattern occurs.**

## Conclusion

This project demonstrates a basic network-traffic analysis workflow in which normal communication is first identified through recognizable protocol behavior, while unusual patterns are then examined for indicators of suspicious activity.

The comparison shows three particularly useful characteristics for distinguishing suspicious traffic:

- **Repetition** — unusually repeated requests or connection attempts
- **Targeting behavior** — repeated targeting of one service or probing of many ports
- **Response behavior** — missing or unsuccessful responses where successful communication would normally be expected

These characteristics can help a security analyst identify traffic that requires further investigation before determining whether an event is actually malicious.
