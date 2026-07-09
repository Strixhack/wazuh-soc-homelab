# IR-007 — Network Port Scanning

**Date:** 2026-04-21
**Severity:** MEDIUM
**Status:** Detected
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Discovery |
| MITRE Technique | T1046 — Network Service Scanning |
| Wazuh Rule | 100008 — Level 10 |
| Source IP | 192.168.56.106 (Kali) |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | Nmap |
| Total Alerts | 1,031 |

---

## Detection

- **Rule 100008** fired on inbound connections from lab attacker IP
- **Sysmon EID 3** captured network connection events
- 1,031 total alerts generated across full port scan

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | SYN packets begin from 192.168.56.106 |
| T+00:01 | Multiple ports probed in rapid succession |
| T+00:02 | Sysmon EID 3 logs inbound connections |
| T+00:03 | Rule 100008 fires — 1,031 total hits |

---

## Investigation

| Field | Value |
|-------|-------|
| Source IP | 192.168.56.106 (Kali attacker) |
| Scan Types | SYN stealth (-sS), Aggressive (-A), Full port (-p-) |
| Tool | Nmap |
| Ports Found Open | 135, 139, 445 |
| Detection | Sysmon EID 3 inbound connections |

---

## Production Note

> Rule 100008 in this lab uses a hardcoded attacker IP (192.168.56.106) for demonstration purposes. In a production environment this rule would use a connection-rate threshold approach — flagging any source IP that connects to more than N distinct destination ports within a defined time window, regardless of source IP.

---

## Impact Assessment

- Attacker mapped open ports and running services
- Reconnaissance data used to plan follow-on attacks
- Open ports 135, 139, 445 identified for exploitation

---

## Containment Steps

1. Block scanning IP at firewall
2. Review and close unnecessary open ports
3. Treat scan as confirmed precursor to attack
4. Increase monitoring on identified open ports

---

## Lessons Learned

- Reconnaissance detected before actual attack providing early warning
- 1,031 alerts provide full visibility into scan scope
- Recommendation: IDS/IPS rules tuned for scan pattern detection
- Recommendation: Minimize attack surface by closing unused ports
