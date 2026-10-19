# Network Reconnaissance and Service Enumeration

Date: 19 October 2026
Target: 127.0.0.1 (authorised local Mac)

## Objective
Identify open TCP ports, enumerate listening services, and assess unnecessary network exposure.

## Commands
```bash
nmap -sT -p 22,80,443,5900,631 127.0.0.1
nmap -sT -sV -p 631 127.0.0.1
sudo lsof -nP -iTCP:631 -sTCP:LISTEN
cupsctl | grep -E '^_(share_printers|remote_admin|remote_any)='
```

## Findings
- Ports 22, 80, 443 and 5900 were closed on localhost.
- TCP port 631 was open.
- Nmap identified IPP / CUPS 2.3 on port 631.
- The cupsd process (PID 303) was listening on all IPv4 and IPv6 interfaces.
- CUPS remote administration was disabled (_remote_admin=0).
- Access from non-local networks was restricted (_remote_any=0).
- Printer sharing was enabled (_share_printers=1).

## Security Assessment
Port 5900 remained closed after previously disabling Screen Sharing. CUPS printer sharing is enabled and should be reviewed to determine whether it is required. Listening on all interfaces does not prove that remote clients can connect; firewall and application access controls require separate verification. No exploitable vulnerability was established by these tests.

## Recommendations
1. Confirm whether printer sharing is necessary.
2. Disable unnecessary sharing features if not required.
3. Maintain macOS security updates.
4. Review firewall rules and permitted network access.

## Lessons Learned
Practised active reconnaissance, TCP port scanning, service enumeration, process inspection and configuration assessment. Distinguished an exposed service from a confirmed vulnerability.
