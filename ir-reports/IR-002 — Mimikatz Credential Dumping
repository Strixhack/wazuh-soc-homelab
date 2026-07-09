# IR-002 — Mimikatz Credential Dumping

**Date:** 2026-04-12
**Severity:** CRITICAL
**Status:** Detected & Contained
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Credential Access |
| MITRE Technique | T1003.001 — LSASS Memory |
| Wazuh Rule | 100002 — Level 14 |
| Target | 192.168.56.105 — DESKTOP-2F01H2Q |
| Tool Used | Mimikatz 2.2.0 |
| Time to Detect | ~2 seconds |

---

## Detection

- **Rule 100002** fired on Mimikatz process creation (Sysmon EID 1)
- **Rule 100001** fired on LSASS memory access (Sysmon EID 10)
- SHA256 hash captured: `61C0810A23580CF492A6BA4F765456108331E7A4134C968C2D6A05261B2D8A1`

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | mimikatz.exe spawned by powershell.exe (Sysmon EID 1) |
| T+00:01 | privilege::debug — SeDebugPrivilege obtained |
| T+00:02 | lsass.exe memory accessed (Sysmon EID 10) |
| T+00:02 | Rule 100002 fires — Level 14 Critical |
| T+00:03 | sekurlsa::logonpasswords executed |

---

## Investigation

- **Malicious Process:** C:\SOC-Lab\mimikatz\x64\mimikatz.exe
- **Parent Process:** powershell.exe
- **Target Process:** lsass.exe (PID from Sysmon logs)
- **User Context:** DESKTOP-2F01H2Q\strix (High Integrity)
- **SHA256:** 61C0810A23580CF492A6BA4F765456108331E7A4134C968C2D6A05261B2D8A1
- **VT Score:** 68/72 engines flagged as malicious

---

## Impact Assessment

- ⚠️ ALL credentials cached on this host are compromised
- ⚠️ NTLM hashes extracted — pass-the-hash attack possible
- ⚠️ Plaintext passwords potentially exposed from memory

---

## Containment Steps

1. Isolate host immediately — disable NIC
2. Kill mimikatz process tree
3. Reset ALL passwords for users logged into host
4. Run `klist purge` to invalidate Kerberos tickets
5. Preserve memory dump for forensics
6. Scan for persistence mechanisms

---

## Lessons Learned

- ✅ Sysmon EID 10 detected LSASS access in real time
- ✅ Alert fired within 2 seconds of execution
- ✅ SHA256 hash matched 68/72 VT engines
- Recommendation: Enable Windows Credential Guard
- Recommendation: Block Mimikatz hash in EDR
- Recommendation: Restrict SeDebugPrivilege to SYSTEM only
