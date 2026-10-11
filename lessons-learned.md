# Lessons Learned — macOS Network Security Assessment

## Project Reflection

This was my first documented cybersecurity home-lab project, completed while studying for CompTIA Security+ SY0-701.

I used Nmap and macOS command-line tools to identify listening network services, investigate their purpose, evaluate potential security risks and verify security improvements.

## Technical Skills Demonstrated

### Network Scanning

Used Nmap to identify open TCP ports and investigate services.

```bash
nmap -sT -Pn 127.0.0.1
nmap -sT -sV -Pn -p 88,631,2968,5000,5900,7000 127.0.0.1
```

**Key lesson:** An open port does not automatically indicate a vulnerability.

### Service Investigation

Used `lsof` and `ps` to identify Epson Event Manager listening on TCP port 2968.

**Key lesson:** Investigate unfamiliar services before deciding whether they present a security risk.

### Attack Surface Reduction

Disabled unnecessary Screen Sharing and confirmed TCP port 5900 was closed.

**Key lesson:** Disabling unnecessary services reduces the potential attack surface.

### Firewall Hardening

Enabled the macOS application firewall and verified its status using Terminal.

**Key lesson:** Security configuration changes should be independently verified.

### Remote Testing

Compared scans originating from the Mac with a scan performed using an iPhone on the same home Wi-Fi network.

**Key lesson:** Local scanning does not prove that a service is reachable from another device. A negative remote scan also requires careful interpretation.

## CompTIA Security+ SY0-701 Topics Applied

| Domain | Relevant Knowledge |
|---|---|
| 1.0 General Security Concepts | Security controls and least functionality |
| 2.0 Threats, Vulnerabilities and Mitigations | Attack surface and exposure assessment |
| 4.0 Security Operations | Monitoring, configuration and remediation verification |

## Challenges and Solutions

**Challenge:** An unfamiliar service appeared on TCP port 2968.

**Solution:** Investigated the associated process and confirmed it was Epson printer software rather than assuming it was malicious.

**Challenge:** Different scan locations produced different results.

**Solution:** Documented the limitations of localhost and second-device scanning without drawing unsupported conclusions.

## Future Development

- Learn Linux log analysis and security monitoring.
- Practise cloud identity and access management.
- Explore Azure security and governance.
- Develop web application security testing skills using OWASP ZAP.

## Professional Takeaway

My security assessment process:

**Discover → Investigate → Assess → Remediate → Verify → Document**

All testing was conducted on devices I own or am authorised to assess.
