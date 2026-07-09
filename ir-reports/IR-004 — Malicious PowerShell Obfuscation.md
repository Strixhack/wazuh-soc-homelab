# IR-004 — Malicious PowerShell Obfuscation

**Date:** 2026-04-13
**Severity:** CRITICAL
**Status:** Detected & Contained
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Defense Evasion + Execution |
| MITRE Technique | T1027 + T1059.001 |
| Wazuh Rules | 100004, 100005 — Level 12 |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | PowerShell built-in |
| Time to Detect | ~1 second |

---

## Detection

- **Rule 100004** fired on -EncodedCommand flag (Sysmon EID 1)
- **Rule 100005** fired on IEX download cradle pattern
- **EID 4104** script block logging captured decoded content

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | Encoded PowerShell command executed |
| T+00:00 | EID 4104 script block logged with decoded content |
| T+00:01 | Rule 100004 fires — encoded command detected |
| T+00:02 | IEX download cradle executed from memory |
| T+00:02 | Rule 100005 fires — download cradle detected |
| T+00:03 | Remote script executed in memory (fileless) |

---

## Investigation

| Field | Value |
|-------|-------|
| Encoded Command | powershell.exe -EncodedCommand [Base64] |
| Decoded Content | Get-LocalUser; Get-Process; whoami |
| Remote Script | http://192.168.56.106:8080/test.ps1 |
| Execution Type | In-memory fileless — no disk artifacts |
| Detection | PowerShell Script Block Logging EID 4104 |

---

## Impact Assessment

- Fileless attack — no disk artifacts to recover
- Remote script executed entirely in memory
- Data enumeration performed on compromised host

---

## Containment Steps

1. Enable PowerShell Constrained Language Mode
2. Block encoded command execution via policy
3. Block remote script URL at proxy
4. Review PowerShell execution policy settings

---

## Lessons Learned

- Script block logging caught fileless attack that left no disk evidence
- Encoded command flagged immediately via PCRE2 pattern matching
- Recommendation: Enable AMSI integration
- Recommendation: PowerShell Just Enough Administration (JEA)
- Recommendation: Block -EncodedCommand via GPO where not required
