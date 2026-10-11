# macOS Network Security Assessment

**A hands-on cybersecurity home lab using Nmap, network enumeration and host firewall hardening.**

## Project Overview

This project documents a security assessment of my personal macOS workstation, conducted as part of my CompTIA Security+ studies.

The objective was to discover network services, identify potential security risks, implement appropriate hardening measures and verify the results.

## Project Documentation

| Document | Description |
|---|---|
| [Nmap Scan Evidence](evidence/nmap-scan-results.md) | Sanitised port scans, service enumeration and verification results |
| [Security Findings Report](findings/security-findings.md) | Risk assessments, remediation actions and recommendations |
| [Lessons Learned](lessons-learned.md) | Practical skills, challenges and Security+ knowledge applied |

### Key Results

- **6 open TCP ports** identified during initial discovery.
- **2 security improvements** implemented and verified.
- **1 printer-related service** investigated.
- **Remote-access exposure reduced** by disabling unnecessary Screen Sharing.
- **Host firewall enabled** and its status verified.

## Tools and Technologies

- Nmap 7.991
- macOS Terminal
- macOS Application Firewall
- `lsof` and `ps`
- TCP/IP networking
- iPhone-based network scanning

## Methodology

1. **Discovery:** Scanned localhost to identify open TCP ports.
2. **Enumeration:** Investigated listening services and associated processes.
3. **Risk assessment:** Evaluated unnecessary services and potential network exposure.
4. **Remediation:** Disabled unnecessary Screen Sharing and enabled the host firewall.
5. **Verification:** Repeated scans and checked firewall status.
6. **Remote testing:** Used an iPhone on the same home network to check whether selected ports were reachable.

## Security Findings

### Finding 001 — Unnecessary Screen Sharing

**Observation:** TCP port 5900 was open and associated with Apple's VNC remote-access service.

**Action:** Disabled Screen Sharing through macOS settings.

**Verification:** A subsequent Nmap scan confirmed port 5900 was closed.

**Status:** Resolved.

### Finding 002 — Epson Event Manager

**Observation:** TCP port 2968 was open.

**Investigation:** Identified the `EEventMan` process using `lsof` and confirmed its executable was located in the Epson Event Manager application directory using `ps`.

**Assessment:** Likely legitimate software associated with an installed Epson printer.

**Remote test:** An iPhone-based scan on the same home Wi-Fi did not detect port 2968 as open. The precise reason for the result was not established.

**Status:** Investigated; remote exposure not demonstrated in the test performed.

### Finding 003 — Disabled macOS Firewall

**Observation:** The built-in application firewall was disabled.

**Action:** Enabled the firewall through macOS System Settings.

**Verification:** Confirmed that the firewall reported `State = 1`.

**Status:** Resolved.

## Security Concepts Demonstrated

- Attack surface reduction
- TCP port scanning
- Service enumeration
- Host-based firewall configuration
- Least privilege and least functionality
- Security remediation and verification
- Local versus remote network accessibility

## Key Lessons Learned

An open port does not automatically indicate malicious activity. Security analysts must identify the service, understand its purpose and evaluate the associated risk before making changes.

I also learned that scanning a computer from localhost does not establish whether the same service is reachable from another device.

## Future Improvements

- Review application-specific firewall permissions.
- Investigate remaining listening services.
- Expand network monitoring and logging.
- Continue documenting practical Security+ exercises.

**All testing was conducted against devices I own or am authorised to assess.**
