# Security Findings Report

**Project:** macOS Network Security Assessment  
**Assessment period:** October 2026  
**Environment:** Personal macOS workstation  
**Assessment type:** Host security configuration and network service review

## Executive Summary

A security assessment was performed on a personal macOS workstation using Nmap, macOS Terminal and the built-in application firewall.

The assessment identified an unnecessary remote-access service, a disabled host firewall and a legitimate printer-related service requiring investigation.

Two security improvements were completed and independently verified. A second-device scan was also performed to assess selected services from the local network.

## Findings Summary

| ID | Finding | Initial Priority | Status |
|---|---|---|---|
| F-001 | Unnecessary Screen Sharing enabled | Medium | Resolved |
| F-002 | Epson Event Manager listening on TCP 2968 | Informational | Reviewed; monitoring recommended |
| F-003 | macOS application firewall disabled | Medium | Resolved |

Priority ratings reflect the limited home-lab scope and are not formal vulnerability severity scores.

---

## F-001 — Unnecessary Screen Sharing Enabled

**Priority:** Medium  
**Status:** Resolved

### Description

Nmap identified TCP port 5900 as open. Service detection associated the port with Apple's VNC remote-access functionality.

Further investigation confirmed that Screen Sharing was enabled even though it was not required.

### Security Impact

Unnecessary remote-access services increase a system's attack surface. Depending on access controls and network exposure, they may provide additional opportunities for unauthorised access.

No unauthorised access or exploitation was observed.

### Remediation

Disabled Screen Sharing in macOS System Settings.

### Verification

Repeated an Nmap scan against TCP port 5900.

**Result:**
```text
5900/tcp closed vnc
```

A later scan of the workstation's Wi-Fi address also reported port 5900 as closed.

### Recommendation

Keep Screen Sharing disabled unless remote access is specifically required. If re-enabled, use strong authentication and appropriate network restrictions.

---

## F-002 — Epson Event Manager Service Investigation

**Priority:** Informational  
**Status:** Reviewed; remote exposure not demonstrated

### Description

Nmap identified TCP port 2968 as open. Process investigation using `lsof` identified `EEventMan`, and `ps` located the executable within the Epson Event Manager application directory.

The workstation owner confirmed that an Epson printer was installed.

### Security Impact

A listening network service can increase the attack surface. However, the existence of a legitimate printer-related service does not establish a security vulnerability.

### Investigation

The service was observed listening on all local IPv4 interfaces.

A localhost scan and a scan initiated from the workstation against its own Wi-Fi address reported TCP port 2968 as open.

An iPhone-based scan from the home Wi-Fi network did not detect the port as open.

### Interpretation

The second-device result did not demonstrate remote reachability. It does not conclusively establish whether the macOS firewall, network isolation or another factor prevented detection.

### Recommendation

- Keep Epson software updated.
- Retain the service if required for printing or scanning.
- Review application firewall permissions when necessary.
- Avoid disabling legitimate services without understanding their operational purpose.

No remediation was required solely on the evidence collected.

---

## F-003 — macOS Application Firewall Disabled

**Priority:** Medium  
**Status:** Resolved

### Description

The macOS application firewall was found disabled during the assessment.

### Security Impact

A disabled host firewall may increase exposure to unwanted inbound network connections, depending on the services listening and the surrounding network controls.

### Remediation

Enabled the macOS application firewall through System Settings.

### Verification

Executed:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
```

**Result:**
```text
Firewall is enabled. (State = 1)
```

Also verified:

```text
Block all DISABLED!
```

The block-all setting being disabled is not itself a finding; macOS can enforce application-specific firewall rules while this setting is off.

### Recommendation

Keep the host firewall enabled, review inbound application permissions and periodically verify the configuration.

---

## Overall Assessment

The assessment resulted in two verified security improvements:

1. Disabled unnecessary Screen Sharing and confirmed the associated port was closed.
2. Enabled the macOS application firewall and verified its active state.

A third investigation identified an expected Epson printer-related process. Its remote accessibility was not demonstrated during the second-device test.

## Limitations

- The assessment was limited to a personally owned workstation.
- Only selected TCP ports were investigated in detail.
- Localhost scanning cannot establish remote network accessibility.
- The iPhone scanning tool did not provide detailed packet-level evidence or a definitive reason for ports not being detected.
- No vulnerability exploitation, credential testing or internet-facing assessment was performed.

## Lessons Learned

This project reinforced the importance of identifying services before changing configurations, applying least-functionality principles, and verifying remediation through repeatable testing.

It also demonstrated the difference between observing a listening service and establishing whether that service is reachable from another network location.
