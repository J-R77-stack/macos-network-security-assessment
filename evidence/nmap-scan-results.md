# Nmap Scan Evidence

**Project:** macOS Network Security Assessment  
**Assessment dates:** 9–10 October 2026  
**Tool:** Nmap 7.991  
**Target:** Personal macOS workstation  
**Scope:** Authorised local testing

## Scan 1 — Initial TCP Port Discovery

**Command:**
```bash
nmap -sT -Pn 127.0.0.1
```

**Recorded results:**
```text
PORT       STATE   SERVICE
88/tcp     open    kerberos-sec
631/tcp    open    ipp
2968/tcp   open    enpp
5000/tcp   open    upnp
5900/tcp   open    vnc
7000/tcp   open    afs3-fileserver
```

**Analysis:** Six TCP ports were initially reported open. Service names in this initial scan are Nmap port-database labels and do not independently establish the identity of the listening software.

## Scan 2 — Service Version Detection

**Command:**
```bash
nmap -sT -sV -Pn -p 88,631,2968,5000,5900,7000 127.0.0.1
```

**Recorded results:**
```text
PORT       STATE    SERVICE   VERSION
88/tcp     closed   kerberos-sec
631/tcp    open     ipp       CUPS 2.3
2968/tcp   open     enpp?
5000/tcp   open     rtsp      AirTunes rtspd 775.3.1
5900/tcp   open     vnc       Apple remote desktop vnc
7000/tcp   open     rtsp      AirTunes rtspd 775.3.1
```

**Analysis:** Version detection identified CUPS, AirTunes and Apple's VNC service. Port 88 was closed by the time this scan was performed.

## Scan 3 — Screen Sharing Remediation

**Action:** Disabled Screen Sharing in macOS settings.

**Verification command:**
```bash
nmap -sT -Pn -p 5900 127.0.0.1
```

**Recorded result:**
```text
PORT       STATE    SERVICE
5900/tcp   closed   vnc
```

**Conclusion:** The VNC port was no longer accepting local TCP connections following remediation.

## Scan 4 — Epson Event Manager Investigation

**Process identification command:**
```bash
sudo lsof -nP -iTCP:2968 -sTCP:LISTEN
```

**Sanitised evidence:**
```text
COMMAND     PID    USER         NAME
EEventMan   503    [REDACTED]   TCP *:2968 (LISTEN)
```

The executable was subsequently identified within the Epson Event Manager application directory using `ps`.

**Local verification:**
```bash
nmap -sT -Pn -p 2968 127.0.0.1
```

**Result:**
```text
2968/tcp   open   enpp
```

**Conclusion:** A locally listening Epson Event Manager service was identified. The presence of this service was consistent with an installed Epson printer.

## Scan 5 — Wi-Fi Interface Verification

**Command:** Scanned the workstation's own Wi-Fi address, redacted below.

```bash
nmap -sT -Pn -p 2968,5900 [MAC_PRIVATE_IP]
```

**Recorded results:**
```text
PORT       STATE    SERVICE
2968/tcp   open     enpp
5900/tcp   closed   vnc
```

**Analysis:** This scan originated from the workstation itself and therefore does not prove remote reachability.

## Scan 6 — Second-Device Test

**Source:** iPhone on home Wi-Fi  
**Target:** Personal macOS workstation  
**Ports:** TCP 2968 and TCP 5900

**Observed result:** The iPhone scanning application reported no open ports for either test.

**Interpretation:** Neither port was detected as open from the iPhone during the test. This is consistent with restricted inbound accessibility, but the precise cause was not determined.

## Firewall Verification

**Commands:**
```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getblockall
```

**Recorded results:**
```text
Firewall is enabled. (State = 1)
Block all DISABLED!
```

**Conclusion:** The macOS application firewall was enabled and verified. The block-all setting was disabled, allowing application-specific firewall behaviour.

## Assessment Summary

- Identified six initially open TCP ports.
- Investigated the software listening on port 2968.
- Disabled unnecessary Screen Sharing.
- Verified TCP port 5900 was closed after remediation.
- Enabled and verified the macOS application firewall.
- Compared local scanning results with a second-device test.

**Security note:** Hostnames, usernames, MAC addresses and private IP addresses have been removed or replaced with placeholders before publication.
