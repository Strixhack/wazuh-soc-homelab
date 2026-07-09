# IR-010 — Event Log Clearing

**Date:** 2026-06-27
**Severity:** CRITICAL
**Status:** Detected — Active Incident
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Defense Evasion |
| MITRE Technique | T1070.001 — Clear Windows Event Logs |
| Wazuh Rules | 100010, 100011 — Level 14 |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | wevtutil.exe (built-in Windows) |
| Time to Detect | ~1 second |

---

## Detection

- **Rule 100010** (Level 14) fired on wevtutil cl command line pattern
- **Rule 100011** (Level 14) fired on Windows Security EID 1102
- **Rule 63103** (Level 5) fired on audit log cleared
- **Rule 63104** (Level 5) fired on Windows log file cleared

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | wevtutil.exe executed (Sysmon EID 1) |
| T+00:01 | Security log cleared — EID 1102 generated |
| T+00:01 | Rule 100010 fires — Level 14 Critical |
| T+00:01 | Rule 100011 fires — Level 14 Critical |
| T+00:02 | System log cleared — EID 104 generated |
| T+00:02 | Application log cleared |
| T+00:03 | PowerShell log cleared |
| T+00:03 | All local Windows logs destroyed |
| T+00:03 | Wazuh SIEM copy fully intact |

---

## Investigation

| Field | Value |
|-------|-------|
| Tool Used | wevtutil.exe (living-off-the-land) |
| Logs Cleared | Security, System, Application, PowerShell |
| EID Generated | 1102 (Security cleared), 104 (System cleared) |
| Intent | Destroy forensic evidence after attack campaign |
| SIEM Status | All logs already forwarded — evidence intact |

---

## Critical Finding

```
Local Windows logs:    DESTROYED by attacker
Wazuh SIEM copy:       FULLY INTACT
All attack evidence:   PRESERVED and searchable
```

Even after clearing all Windows event logs, every alert remained preserved in Wazuh. The attacker destroyed local evidence but the SIEM had already forwarded and indexed all events. This proves the core value of centralized logging — the SIEM is the last line of forensic defense.

---

## Impact Assessment

- All local Windows event logs permanently destroyed
- Attacker attempted to eliminate forensic trail
- Log clearing is a confirmed active breach indicator
- SIEM evidence fully preserved — investigation can proceed

---

## Containment Steps

1. Isolate host IMMEDIATELY — this is a confirmed breach indicator
2. Preserve memory dump before any shutdown
3. Begin full forensic investigation using Wazuh SIEM logs
4. Treat as confirmed breach — initiate full IR response
5. Restrict wevtutil.exe via AppLocker going forward
6. Review what actions preceded the log clearing

---

## Lessons Learned

- Wazuh retained all forwarded logs — forensic evidence preserved
- Alert fired within 1 second of log clearing activity
- Log clearing confirms attacker was aware of detection
- Recommendation: Forward all logs to SIEM in real time
- Recommendation: Alert immediately on any log clearing event
- Recommendation: Restrict wevtutil.exe via AppLocker
- Recommendation: SIEM proves its value — local logs cannot be trusted after breach
