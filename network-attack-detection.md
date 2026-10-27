# Day 12 — Network Attack Detection and DNS Security Assessment

**Certification:** CompTIA Security+ SY0-701  
**Date:** 27 October 2026  
**Environment:** Intel MacBook Air, macOS  
**Assessment type:** Defensive network security investigation   
**Practical lab:** Completed

---

## 1. Executive Summary

Conducted a defensive security assessment of a home network to investigate potential indicators of ARP spoofing, Man-in-the-Middle (MITM) attacks, DNS poisoning and network traffic redirection.

The investigation involved inspecting the ARP table, verifying the default gateway, comparing DNS responses from independent resolvers, examining DNSSEC information and checking the stability of the gateway's MAC address.

**Overall finding:** No clear indicators of ARP spoofing or DNS poisoning were identified in the collected evidence.

The assessment was limited to periodic command-line observations and did not include continuous packet capture or active attack simulation.

## 2. Objectives

- Identify devices and IP-to-MAC mappings on the local network.
- Verify the default gateway and its MAC address.
- Investigate possible ARP spoofing indicators.
- Inspect the configured DNS resolvers.
- Compare DNS responses from different resolvers.
- Examine DNSSEC signatures and validation flags.
- Document findings and security recommendations.

## 3. Tools and Technologies

| Tool | Purpose |
|---|---|
| `arp` | Inspect local IP-to-MAC address mappings |
| `route` | Identify the default gateway |
| `scutil` | Inspect macOS DNS configuration |
| `dig` | Query DNS records and examine responses |
| `dscacheutil` | Inspect macOS hostname resolution |
| macOS Terminal | Execute investigation commands |

All investigation commands were used for defensive analysis. No attack traffic was generated.

## 4. Investigation and Evidence

### 4.1 ARP Table Investigation

**Command:**

```bash
arp -a
```

The ARP table contained entries for several local devices, including:

| IP address | MAC address | Observed identification |
|---|---|---|
| 192.168.1.115 | 1a:76:6a:65:86:80 | iPhone |
| 192.168.1.222 | 98:25:4a:a3:de:cc | TP-Link network device |
| 192.168.1.254 | d4:35:1d:5c:f6:a9 | Default gateway |

Two additional local IPv4 entries and one multicast DNS entry were also observed.

**Finding:** No obviously suspicious IP-to-MAC mappings were identified in the initial ARP snapshot.

**Limitation:** An ARP table snapshot alone cannot prove that ARP spoofing is absent.

### 4.2 Default Gateway Identification

**Command:**

```bash
route -n get default
```

**Observed results:**

```text
gateway: 192.168.1.254
interface: en0
mtu: 1500
```

The default gateway matched the router entry previously identified in the ARP table.

### 4.3 Gateway MAC Address Verification

**Command:**

```bash
arp -n 192.168.1.254
```

**Observed result:**

```text
? (192.168.1.254) at d4:35:1d:5c:f6:a9 on en0 ifscope [ethernet]
```

The gateway's MAC address was checked at different stages of the investigation.

**Finding:** The observed gateway MAC address remained unchanged.

This provided no evidence of a gateway MAC mapping change during the assessment.

### 4.4 DNS Configuration Investigation

**Command:**

```bash
scutil --dns
```

**Observed configuration:**

```text
search domain: lan
nameserver: 192.168.1.254
interface: en0
```

The Mac was configured to use the local router as its DNS server.

Additional multicast DNS resolver entries were present, consistent with macOS local network discovery.

**Finding:** No obviously unexpected DNS server configuration was identified.

### 4.5 DNS Resolution Through the Home Router

**Command:**

```bash
dig example.com
```

**Observed results:**

```text
status: NOERROR
example.com. 940 IN A 172.66.147.243
example.com. 940 IN A 104.20.23.154

Query time: 132 msec
SERVER: 192.168.1.254#53
```

The query returned two IPv4 addresses.

### 4.6 Independent DNS Resolver Comparison

**Command:**

```bash
dig @1.1.1.1 example.com
```

**Observed results:**

```text
status: NOERROR
example.com. 5 IN A 104.20.23.154
example.com. 5 IN A 172.66.147.243

Query time: 104 msec
SERVER: 1.1.1.1#53
```

**Comparison:**

| DNS resolver | Returned IPv4 addresses |
|---|---|
| Home router | 172.66.147.243, 104.20.23.154 |
| Cloudflare | 104.20.23.154, 172.66.147.243 |

Both resolvers returned the same set of IPv4 addresses.

Differences in address ordering and TTL values can occur normally due to DNS caching and response behaviour.

**Finding:** No discrepancy in the returned IPv4 address sets was identified.

**Limitation:** Matching responses do not independently establish that DNS poisoning is impossible.

### 4.7 DNSSEC Investigation

**Command:**

```bash
dig @1.1.1.1 cloudflare.com A +dnssec
```

**Observed evidence:**

- DNS response status: `NOERROR`
- Authenticated Data (`AD`) flag present
- DNSSEC OK (`DO`) flag present
- `RRSIG` record returned
- DNSSEC signature algorithm: 13

**Analysis:**

The `RRSIG` record contains a digital signature associated with DNS records.

The `AD` flag indicates that the responding resolver reports the DNS data as authenticated.

The `DO` flag indicates support for receiving DNSSEC records in the query and response exchange.

**Finding:** DNSSEC-related records and resolver-reported validation were successfully observed.

**Limitation:** The Mac did not independently verify the DNSSEC chain of trust during this exercise. The command did not encrypt the DNS query.

### 4.8 DNS Cache Statistics

**Command:**

```bash
sudo dscacheutil -statistics
```

**Observed output:**

```text
Unable to get details from the cache node
```

DNS cache statistics were unavailable through this command.

This was documented as a tool limitation, not an indicator of compromise.

### 4.9 macOS System Resolver Investigation

**Command:**

```bash
dscacheutil -q host -a name example.com
```

**Observed IPv4 addresses:**

```text
104.20.23.154
172.66.147.243
```

**Observed IPv6 addresses:**

```text
2606:4700:10::ac42:93f3
2606:4700:10::6814:179a
```

The IPv4 results matched the earlier DNS queries.

This demonstrated consistent hostname resolution across the tested methods.

### 4.10 Final ARP Verification

**Command:**

```bash
arp -n 192.168.1.254
```

**Final result:**

```text
? (192.168.1.254) at d4:35:1d:5c:f6:a9 on en0 ifscope [ethernet]
```

The gateway MAC address remained consistent with the initial observation.

**Finding:** No gateway MAC address change was detected during the assessment.

---

## 5. Security Findings

| Investigation | Result |
|---|---|
| ARP table inspection | No obvious suspicious mappings |
| Gateway identification | Successfully verified |
| Gateway MAC stability | No observed changes |
| DNS configuration | Consistent with home router |
| DNS resolver comparison | Matching IPv4 address sets |
| DNSSEC investigation | RRSIG and validation flags observed |
| macOS hostname resolution | Consistent with earlier DNS results |
| DNS cache statistics | Unavailable |

**Overall assessment:** No clear evidence of ARP spoofing, DNS poisoning or unauthorised DNS configuration was identified within the scope of the investigation.

## 6. Security Recommendations

1. **Router security:** Keep router firmware updated and protect the administration interface with strong credentials.
2. **Wireless security:** Use WPA2-AES or WPA3 encryption.
3. **DNS security:** Consider a trusted DNSSEC-validating resolver and encrypted DNS where appropriate.
4. **HTTPS protection:** Use HTTPS with valid certificate verification to reduce the impact of traffic interception.
5. **Network monitoring:** Investigate unexpected changes in gateway MAC addresses or configured DNS servers.
6. **Enterprise network controls:** Where supported, use DHCP snooping and Dynamic ARP Inspection to mitigate relevant local network attacks.

## 7. Skills Demonstrated

- Network reconnaissance for defensive assessment
- ARP table analysis
- Default gateway verification
- Identification of possible MITM indicators
- DNS configuration analysis
- DNS resolver comparison
- DNSSEC record inspection
- IPv4 and IPv6 hostname resolution
- Evidence-based security reporting
- Recognition of investigation limitations

## 8. Security+ Knowledge Reinforced

**ARP spoofing:** An attacker attempts to associate their MAC address with another device's IP address to redirect local network traffic.

**DNS poisoning:** Malicious or incorrect DNS information can redirect users to unintended destinations.

**Man-in-the-Middle attacks:** An attacker intercepts or manipulates communications between systems.

**DNSSEC:** Provides mechanisms for authenticating DNS data and detecting unauthorised modifications, but does not provide DNS query confidentiality.

**TLS:** Protects application communications through encryption and server authentication when correctly implemented and validated.

## 9. Conclusion

This assessment provided practical experience investigating network attack indicators using built-in macOS tools.

The investigation confirmed that:

- The default gateway was identified.
- The observed gateway MAC address remained stable.
- The tested DNS resolvers returned matching IPv4 address sets.
- DNSSEC signatures and validation-related flags were observed.
- The macOS system resolver returned consistent results.

No clear indicators of ARP spoofing or DNS poisoning were identified.

The investigation demonstrated the importance of comparing multiple sources of evidence, documenting tool limitations and avoiding unsupported security conclusions.

**Assessment status: Completed successfully.**

---

*Part of my CompTIA Security+ SY0-701 practical cybersecurity portfolio.*
