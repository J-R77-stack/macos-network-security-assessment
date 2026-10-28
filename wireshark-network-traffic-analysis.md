# Wireshark Network Traffic Analysis and Security Investigation

**Certification:** CompTIA Security+ SY0-701  
**Date:** 28 October 2026  
**Environment:** Intel MacBook Air, macOS  
**Tool:** Wireshark  
**Assessment type:** Defensive packet capture and network traffic analysis  
**Status:** Completed

---

## 1. Executive Summary

Conducted a practical network traffic investigation using Wireshark to examine TCP connection establishment, TLS encryption, connection resets, retransmissions and DNS communications.

The investigation involved capturing live network packets, applying protocol-specific display filters, following a TCP conversation and examining packet-level evidence.

**Overall finding:** The examined traffic showed successful TCP and TLS handshakes, encrypted application communications, a small number of connection resets and retransmission-related packets, and a DNS query with a matching response.

No clear evidence of malicious activity was identified in the examined traffic.

## 2. Investigation Objectives

- Capture live network traffic on macOS.
- Identify common network protocols.
- Apply Wireshark display filters.
- Investigate a TCP three-way handshake.
- Examine TLS handshake information.
- Identify a hostname using Server Name Indication (SNI).
- Investigate TCP reset and retransmission packets.
- Capture and analyse a DNS query and response.
- Document evidence and limitations.

## 3. Tools and Environment

| Component | Description |
|---|---|
| Operating system | macOS |
| Network interface | en0 |
| Packet analysis tool | Wireshark |
| Command-line tool | Terminal and dig |
| Investigation method | Passive packet inspection and controlled DNS query |

All activity was performed on the local computer and its network traffic.

## 4. TCP Connection Investigation

### 4.1 Identifying TCP SYN Packets

Applied the Wireshark display filter:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

Identified an initial TCP SYN packet:

```text
Packet: 17
Source: 192.168.1.151
Destination: 3.251.217.112
Protocol: TCP
Source port: 56629
Destination port: 443
Flags: SYN
Payload length: 0
```

The packet indicated that the local computer was attempting to establish a TCP connection with an external server on port 443.

### 4.2 TCP Three-Way Handshake

Used Wireshark's **Conversation Filter → TCP** feature to isolate the connection.

Observed the following sequence:

1. SYN — Client initiated the connection.
2. SYN, ACK — Server acknowledged the request.
3. ACK — Client acknowledged the server's response.

**Finding:** The TCP three-way handshake completed successfully.

## 5. TLS Security Investigation

### 5.1 TLS Handshake

After the TCP handshake, observed:

- Client Hello
- Server Hello
- Encrypted application data

The Server Hello indicated that the connection negotiated **TLS 1.3**.

The initial appearance of TLS 1.2 was associated with the legacy version field used for compatibility.

### 5.2 Server Name Indication

Inspected the Client Hello packet and identified the SNI hostname:

```text
unagi.amazon.co.uk
```

The connection evidence included:

| Field | Value |
|---|---|
| Destination IP | 3.251.217.112 |
| Destination port | 443 |
| SNI hostname | unagi.amazon.co.uk |
| Negotiated TLS version | TLS 1.3 |

**Finding:** The client requested a TLS connection using an Amazon-related hostname.

**Limitation:** SNI information alone does not establish the legitimacy or trustworthiness of the destination.

### 5.3 Encrypted Application Data

Observed application data packets following the TLS handshake.

Wireshark displayed packet metadata, while the application payload remained encrypted.

**Security relevance:** Analysts can examine connection behaviour and metadata even when application content is unavailable.

## 6. TCP Reset Investigation

Applied the filter:

```text
tcp.flags.reset == 1
```

Identified two reset packets:

```text
Packet 690
Time: 7.791538
Source: 17.253.29.151
Destination: 192.168.1.151
Ports: 443 → 61207
Flags: RST
Sequence: 2

Packet 691
Time: 7.795937
Source: 17.253.29.151
Destination: 192.168.1.151
Ports: 443 → 61207
Flags: RST
Sequence: 2
```

Both packets originated from the same remote IP address and targeted the same local port.

The source address falls within an Apple-associated IP address range, although the specific service was not independently verified.

### 6.1 Connection Termination Analysis

Examined surrounding packets:

| Packet | Observation |
|---|---|
| 684 | Server sent FIN, ACK |
| 686 | TLS application data flagged as out-of-order |
| 689 | Client sent FIN, ACK |
| 690 | Server sent RST |
| 691 | Server sent RST |

The sequence showed connection closure activity followed by reset packets.

**Finding:** The resets were not sufficient evidence of malicious activity.

Additional traffic analysis would be required to determine the precise reason for the resets.

## 7. TCP Retransmission Investigation

Applied the filter:

```text
tcp.analysis.retransmission
```

Three retransmission-related packets were identified.

| Packet | Source IP | Destination port | Observation |
|---|---|---|---|
| 114 | 172.64.155.209 | 64238 | TCP retransmission |
| 597 | 172.64.155.209 | 64238 | TCP fast retransmission |
| 705 | 3.251.217.112 | 56629 | TCP retransmission |

Packet 705 belonged to the previously investigated Amazon-related TCP connection.

The other two packets were associated with a separate connection.

**Finding:** Retransmission-related traffic was present, but the limited evidence did not establish a significant network reliability problem or security incident.

Wireshark's retransmission classifications are analytical interpretations and should be assessed in context.

## 8. DNS Packet Investigation

### 8.1 Generating DNS Traffic

Started a new Wireshark capture on interface `en0`.

Executed the following Terminal command:

```bash
dig @1.1.1.1 example.org A
```

This generated a DNS query requesting IPv4 records for `example.org`.

### 8.2 DNS Display Filter

Applied:

```text
dns && ip.addr == 1.1.1.1
```

Two packets were observed, representing the DNS request and response.

### 8.3 DNS Transaction Analysis

The DNS query contained transaction ID:

```text
0xdd12
```

The response contained the same transaction ID.

**Finding:** The response identifier matched the query identifier.

A matching transaction ID is expected in a normal DNS exchange but does not independently authenticate the response.

### 8.4 DNS Answer Records

The DNS response contained two IPv4 addresses:

```text
172.66.157.237
104.20.26.136
```

These were the addresses returned for `example.org` during the investigation.

**Finding:** The controlled DNS lookup completed successfully, and the query-response exchange was visible at packet level.

## 9. Security Findings

| Investigation | Result |
|---|---|
| Live packet capture | Successful |
| TCP handshake | Completed |
| TLS handshake | TLS 1.3 identified |
| SNI investigation | Hostname identified |
| Encrypted application data | Observed |
| TCP resets | Two packets investigated |
| Retransmission-related packets | Three identified |
| DNS query and response | Captured |
| DNS transaction ID | Matching |
| Clear indicators of malicious activity | None identified in examined evidence |

## 10. Security Analysis

This investigation demonstrated how Wireshark can support network security monitoring and incident investigation.

Important lessons included:

- TCP handshakes help establish whether connections are being initiated successfully.
- TLS protects application data, but network metadata remains useful for analysis.
- SNI can reveal the hostname requested during a conventional TLS handshake.
- TCP resets can occur during ordinary connection termination.
- Retransmissions may indicate packet loss or other network conditions but are not automatically malicious.
- DNS transaction IDs help associate responses with queries.
- Packet analysis requires context before security conclusions can be drawn.

## 11. Limitations

- The initial capture was short.
- The first capture was not saved as a PCAP file.
- Investigation findings were documented from observed packet details.
- No continuous traffic monitoring was performed.
- No malicious traffic was intentionally generated.
- No application payload decryption was attempted.
- The absence of suspicious indicators does not prove that the network is free from threats.

## 12. Skills Demonstrated

- Wireshark packet capture
- Protocol identification
- TCP display filtering
- TCP handshake analysis
- TLS version identification
- SNI hostname inspection
- TCP reset investigation
- Retransmission analysis
- DNS query and response inspection
- Evidence collection
- Technical security reporting

## 13. Conclusion

Successfully completed a defensive packet analysis investigation using Wireshark on macOS.

The assessment demonstrated practical understanding of TCP connection establishment, TLS encryption, TCP connection termination, retransmissions and DNS communications.

The investigation reinforced the importance of interpreting network evidence accurately and distinguishing normal network behaviour from potential security threats.

**Assessment status: Completed successfully.**

---

*Part of my CompTIA Security+ SY0-701 practical cybersecurity portfolio.*
