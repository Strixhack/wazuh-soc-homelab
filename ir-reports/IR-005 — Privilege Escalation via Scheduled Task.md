# IR-005 — Privilege Escalation via Scheduled Task

**Date:** 2026-04-13
**Severity:** CRITICAL
**Status:** Detected & Contained
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Privilege Escalation |
| MITRE Technique | T1548.002 + T1053.005 |
| Wazuh Rules | 100006, 100013 — Level 12-14 |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | schtasks.exe (built-in Windows) |
| Time to Detect | ~1 second |

---

## Detection

- **Rule 100006** fired on schtasks /create /ru SYSTEM (Sysmon EID 1)
- **Rule 100013** fired on scheduled task creation (Windows Security EID 4698)
- Output file confirmed NT AUTHORITY\SYSTEM execution

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | schtasks.exe creates task as SYSTEM (EID 4698) |
| T+00:01 | Rule 100006 fires — task creation detected |
| T+00:01 | Rule 100013 fires — EID 4698 alert |
| T+00:02 | cmd.exe spawned as NT AUTHORITY\SYSTEM |
| T+00:03 | Output file confirms SYSTEM privilege obtained |

---

## Investigation

| Field | Value |
|-------|-------|
| Task Name | WindowsUpdate |
| Command | cmd.exe /c whoami > C:\SOC-Lab\output.txt |
| Run As | NT AUTHORITY\SYSTEM |
| Result | nt authority\system confirmed |
| Detection | Sysmon EID 1 + Windows Security EID 4698 |

---

## Impact Assessment

- SYSTEM level code execution achieved
- Highest Windows privilege level obtained
- Full control over target system

---

## Containment Steps

1. Delete malicious scheduled task immediately
2. Audit all scheduled tasks for further persistence
3. Restrict schtasks.exe via AppLocker
4. Enforce least privilege policy across all accounts
5. Review all SYSTEM-level scheduled tasks

---

## Lessons Learned

- SYSTEM task creation detected within 1 second
- EID 4698 captured full task creation details
- Recommendation: AppLocker policy to restrict schtasks.exe
- Recommendation: Least privilege enforcement across all accounts
- Recommendation: Alert on all scheduled task creation events
