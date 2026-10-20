# Network Attack Detection and DNS Security Assessment

## 1. Objective

Investigate the network configuration of a macOS system, establish a security baseline and examine indicators associated with ARP spoofing, DNS poisoning and rogue DHCP activity.

Explore DNSSEC and defensive security controls relevant to the CompTIA Security+ SY0-701 certification.

## 2. Lab Environment

- **Operating system:** macOS
- **Network interface:** en0
- **Default gateway:** 192.168.1.254 (private IP address)
- **DNS resolver:** 192.168.1.254
- **Tools:** Terminal, arp, route, scutil and dig
- **Scope:** Authorised investigation of my own Mac and home network

All commands were read-only. No attacks were performed.

Device MAC addresses have been redacted for privacy.

## 3. ARP Table Investigation

**Command:**

```bash
arp -a
```

**Findings:**

| IP Address | MAC Address | Description |
|---|---|---|
| 192.168.1.150 | REDACTED | Local network device |
| 192.168.1.222 | REDACTED | Local network extender |
| 192.168.1.254 | REDACTED | Default gateway |
| 224.0.0.251 | 01:00:5e:00:00:fb | Standard multicast DNS mapping |

**Security analysis:**

The default gateway's IP-to-MAC mapping was identified as a baseline for future comparison.

No obvious evidence of ARP spoofing was observed. However, a single ARP table snapshot cannot independently establish that the gateway mapping is legitimate.

## 4. Default Gateway Verification

**Command:**

```bash
route -n get default
```

**Findings:**

- Default gateway: 192.168.1.254
- Interface: en0
- MTU: 1500

**Security analysis:**

The gateway IP address matched the entry identified in the ARP table.

This demonstrated consistency between the Mac's routing and ARP information.

## 5. DNS Configuration Investigation

**Command:**

```bash
scutil --dns | grep 'nameserver\['
```

**Findings:**

Both displayed DNS resolver entries pointed to 192.168.1.254.

**Security analysis:**

The Mac was configured to use the local router for DNS resolution.

No unexpected DNS resolver addresses appeared in the displayed results.

## 6. DNS Resolution Test

**Command:**

```bash
dig example.com
```

**Findings:**

- DNS status: NOERROR
- DNS server: 192.168.1.254
- Query time: 50 ms
- IPv4 addresses returned:
  - 172.66.147.243
  - 104.20.23.154
- TTL: 1246 seconds

**Security analysis:**

The DNS query completed successfully and returned two A records.

Multiple IPv4 addresses can be expected for websites using distributed infrastructure.

Successful DNS resolution does not independently prove that the returned data was authentic.

## 7. DNSSEC Investigation

**Command:**

```bash
dig +dnssec cloudflare.com
```

**Findings:**

- DNS status: NOERROR
- DNS server: 192.168.1.254
- Query time: 57 ms
- DNSSEC OK (DO) flag: Present
- RRSIG record: Present
- Authenticated Data (AD) flag: Absent

**Security analysis:**

The response included an RRSIG record, indicating that DNSSEC signature information was returned.

However, the absence of the AD flag means the response did not indicate successful DNSSEC validation by the resolver.

Receiving a digital signature does not itself establish that the signature was verified.

## 8. Network Attacks and Defensive Controls

| Attack | Potential Indicator | Recommended Defence |
|---|---|---|
| ARP spoofing | Unexpected gateway MAC address changes | Dynamic ARP Inspection |
| Rogue DHCP server | Unauthorised DHCP offers | DHCP snooping |
| DNS poisoning | Unexpected or forged DNS responses | DNSSEC validation |
| TCP SYN flood | Excessive incomplete TCP handshakes | SYN cookies |
| DDoS | High traffic volumes from multiple sources | DDoS mitigation and traffic filtering |
| Evil Twin Wi-Fi | Access point impersonating a legitimate SSID | Strong wireless authentication and rogue AP detection |

## 9. Security Recommendations

1. Maintain a trusted baseline of gateway IP-to-MAC mappings.
2. Investigate unexpected DNS resolver changes or suspicious website redirects.
3. Use DNSSEC-validating resolvers where available.
4. Deploy DHCP snooping and Dynamic ARP Inspection on compatible managed enterprise switches.
5. Use IDS/IPS solutions to detect and prevent suspicious network activity.
6. Correlate evidence from multiple sources before confirming a security incident.
7. Redact device identifiers and other unnecessary sensitive information from public reports.

## 10. Conclusion

This assessment established a baseline of the Mac's local ARP, routing and DNS configuration.

The observed settings were internally consistent, and DNS queries completed successfully.

A DNSSEC signature record was returned during testing, but successful DNSSEC validation was not confirmed.

No network attack was confirmed during this investigation.

The practical exercises strengthened understanding of network reconnaissance, DNS security, Layer 2 threats and evidence-based security analysis.

## 11. CompTIA Security+ Learning Outcomes

- DoS and DDoS attacks
- SYN floods and SYN cookies
- On-path attacks and Evil Twin wireless networks
- ARP spoofing and Dynamic ARP Inspection
- DNS poisoning and DNSSEC
- Rogue DHCP servers and DHCP snooping
- IDS versus IPS
- Incident response and containment
- False positives and false negatives
- macOS network investigation


**Assessment:** Personal cybersecurity home lab

**GitHub portfolio:** J-R77-stack
