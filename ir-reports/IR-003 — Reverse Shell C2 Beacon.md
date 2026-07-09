# IR-003 — Reverse Shell / C2 Beacon

**Date:** 2026-04-13
**Severity:** CRITICAL
**Status:** Detected & Contained
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Execution + Command and Control |
| MITRE Technique | T1059.001 + T1105 + T1071 |
| Wazuh Rule | 100003 — Level 12 |
| Source IP | 192.168.56.106 (Kali) |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | Metasploit / msfvenom |
| Time to Detect | ~2 seconds |

---

## Detection

- **Rule 100003** fired on outbound TCP:4444 connection (Sysmon EID 3)
- **Sysmon EID 1** captured update.exe process creation
- **Sysmon EID 11** captured file creation in C:\SOC-Lab

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | update.exe downloaded via SMB (Sysmon EID 11) |
| T+00:01 | update.exe executed (Sysmon EID 1) |
| T+00:02 | Outbound TCP:4444 to 192.168.56.106 (Sysmon EID 3) |
| T+00:02 | Rule 100003 fires — C2 beacon detected |
| T+00:03 | Meterpreter session opened on Kali attacker |

---

## Investigation

| Field | Value |
|-------|-------|
| Malicious File | C:\SOC-Lab\update.exe |
| Download Source | \\192.168.56.106\share |
| C2 Server | 192.168.56.106:4444 |
| Protocol | Meterpreter TCP reverse shell |
| Parent Process | powershell.exe |
| Detection | Sysmon EID 3 outbound port 4444 |

---

## Impact Assessment

- Full remote access established
- Attacker had interactive shell on Windows VM
- Risk of credential dumping, lateral movement, persistence

---

## Containment Steps

1. Kill update.exe process immediately
2. Block outbound port 4444 at firewall
3. Delete malicious binary
4. Block C2 IP 192.168.56.106
5. Scan for persistence mechanisms
6. Check for lateral movement attempts

---

## Lessons Learned

- Sysmon EID 3 caught outbound C2 connection immediately
- Non-standard port 4444 flagged by Rule 100003
- Recommendation: Block all non-standard outbound ports
- Recommendation: Implement application whitelisting
- Recommendation: Proxy all outbound web traffic
