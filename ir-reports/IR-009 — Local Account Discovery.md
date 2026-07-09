# IR-009 — Local Account Discovery

**Date:** 2026-06-27
**Severity:** LOW
**Status:** Detected
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Discovery |
| MITRE Technique | T1087.001 — Local Account |
| Wazuh Rule | 100009 — Level 8 |
| Source | 192.168.56.106 (Kali) + Windows VM locally |
| Target | 192.168.56.105 (Windows VM) |
| Tool Used | net.exe, PowerShell, CrackMapExec |

---

## Detection

- **Rule 100009** fired on net.exe process creation (Sysmon EID 1)
- Pattern matched net.exe with user/localgroup arguments
- Remote SMB enumeration from Kali also captured

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | net.exe spawned — local user enumeration begins |
| T+00:01 | Sysmon EID 1 logs net.exe process creation |
| T+00:02 | Rule 100009 fires — enumeration detected |
| T+00:03 | Remote CrackMapExec enumeration via SMB detected |

---

## Investigation

| Field | Value |
|-------|-------|
| Commands Executed | net user, net localgroup, Get-LocalUser |
| Remote Source | 192.168.56.106 via SMB |
| Credentials Used | labuser credentials (previously compromised) |
| Data Exposed | Full user list and group memberships |
| Detection | Sysmon EID 1 net.exe command line match |

---

## Impact Assessment

- Full local user list exposed to attacker
- Group memberships revealed (including Administrators)
- Data used to identify high-value accounts for targeting

---

## Containment Steps

1. Disable compromised labuser account
2. Reset all credentials on affected host
3. Restrict net.exe via AppLocker where not required
4. Review what user data was exposed to attacker

---

## Lessons Learned

- Enumeration activity detected correctly via Sysmon EID 1
- Remote SMB enumeration also captured in same detection
- Recommendation: Principle of least privilege across all accounts
- Recommendation: Disable all unused test accounts after lab sessions
- Recommendation: Restrict net.exe usage via AppLocker policy
