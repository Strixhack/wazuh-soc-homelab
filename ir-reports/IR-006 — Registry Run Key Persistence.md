# IR-006 — Registry Run Key Persistence

**Date:** 2026-04-20
**Severity:** CRITICAL
**Status:** Detected & Remediated
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Persistence |
| MITRE Technique | T1547.001 — Registry Run Keys / Startup Folder |
| Wazuh Rules | 92302 (built-in), 100007 (custom) |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | reg.exe (built-in Windows) |
| Time to Detect | ~1 second |

---

## Detection

Two rules fired independently providing defense-in-depth:

- **Rule 92302** (Wazuh built-in, Level 6) — detected reg.exe command line pattern modifying Run key
- **Rule 100007** (custom, Level 10) — detected Sysmon EID 13 kernel-level registry write event

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | reg.exe executed (Sysmon EID 1) |
| T+00:01 | Registry Run key value set (Sysmon EID 13) |
| T+00:01 | Rule 92302 fires — command line pattern match |
| T+00:01 | Rule 100007 fires — EID 13 registry event |
| T+00:02 | Entry confirmed — persists across reboots |

---

## Investigation

| Field | Value |
|-------|-------|
| Registry Key | HKU\S-1-5-21-...\CurrentVersion\Run\SecurityUpdate |
| Malicious Value | C:\SOC-Lab\update.exe |
| Image | C:\Windows\system32\reg.exe |
| Event Type | SetValue |
| Detection | Sysmon EID 13 + reg.exe command line |

---

## Impact Assessment

- Malware persists across reboots
- Payload executes on every user login
- Attacker maintains long-term access

---

## Containment Steps

1. Delete malicious registry key immediately
2. Audit all Run/RunOnce keys for further persistence
3. Investigate update.exe binary with VirusTotal
4. Full malware scan on host

---

## Lessons Learned

- Two independent rules provide defense-in-depth detection
- Sysmon EID 13 catches kernel-level write regardless of tool used
- Rule 92302 catches command-line based modifications
- Recommendation: Monitor all autorun registry locations
- Recommendation: Application whitelisting to block unknown executables
