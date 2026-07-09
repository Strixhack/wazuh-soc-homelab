# IR-008 — File Integrity Violation

**Date:** 2026-05-19
**Severity:** HIGH
**Status:** Detected & Investigated
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Impact |
| MITRE Technique | T1565.001 — Stored Data Manipulation |
| Wazuh Rules | 550 (modified), 554 (new file) — Level 7/5 |
| Target | 192.168.56.105 — C:\Temp\config.txt |
| Detection | Wazuh FIM (File Integrity Monitoring) |
| Total Alerts | 281 |

---

## Detection

- **Rule 554** (Level 5) fired on new file creation in monitored directory
- **Rule 550** (Level 7) fired on file modification — SHA256 hash mismatch confirmed tampering

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | FIM baseline established for C:\Temp |
| T+03:00 | config.txt created — Rule 554 fires (new file) |
| T+03:15 | config.txt modified — hash mismatch detected |
| T+03:15 | Rule 550 fires — integrity violation confirmed |

---

## Investigation

| Field | Value |
|-------|-------|
| Modified File | c:\temp\config.txt |
| Old MD5 | a71bb6374de4d51d4913ed2ef67b9c6a |
| New MD5 | e4132a01c3037984759996ca9c64d716d |
| SHA256 Before | 1a3fe07dbe5f5ebde73cdfb4070abd6c5f09d521a43d6a84c2e951613da5853a |
| SHA256 After | 8ce27fc9334c4783d84efe4ab3bf90502226a5a7429cced682e39e8cce0ab083b |
| FIM Mode | realtime |
| Changed Attributes | size, mtime, md5, sha1, sha256 |
| Total FIM Alerts | 281 |

---

## Impact Assessment

- Config file tampered — cryptographic proof via hash comparison
- Sensitive data modified by unauthorized process
- System integrity compromised

---

## Containment Steps

1. Restore file from clean backup
2. Investigate who modified the file and when
3. Review access permissions on sensitive paths
4. Extend FIM monitoring to additional directories

---

## Lessons Learned

- FIM detected tampering within 15 seconds using realtime mode
- SHA256 hash comparison provides cryptographic evidence of tampering
- 281 total FIM alerts generated across full simulation
- Recommendation: Extend FIM to all sensitive config directories
- Recommendation: Apply read-only permissions to config files
- Recommendation: Centralised logging means hash evidence is preserved even if attacker deletes files
