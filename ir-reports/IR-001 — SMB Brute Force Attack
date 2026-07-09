# IR-001 — SMB Brute Force Attack

**Date:** 2026-03-25
**Severity:** HIGH
**Status:** Detected & Contained
**Analyst:** Kundan Vidhate

---

## Incident Summary

| Field | Details |
|-------|---------|
| MITRE Tactic | Credential Access |
| MITRE Technique | T1110.002 — Password Cracking |
| Wazuh Rule | 60204 — Level 10 |
| Source IP | 192.168.56.106 (Kali) |
| Target | 192.168.56.105 — labuser via SMB port 445 |
| Tool Used | CrackMapExec |
| Time to Detect | ~3 minutes |

---

## Detection

- **Rule 60204** fired on multiple Windows logon failures (EID 4625)
- **Rule 60137** fired on account lockout (EID 4740)
- **Rule 60122** fired on each individual logon failure

---

## Attack Timeline

| Time | Event |
|------|-------|
| T+00:00 | CrackMapExec initiates SMB connections from 192.168.56.106 |
| T+00:01 | EID 4625 — Multiple STATUS_LOGON_FAILURE responses |
| T+00:03 | Rule 60204 fires — brute force threshold crossed |
| T+00:05 | EID 4740 — Account lockout triggered |
| T+00:05 | Rule 60137 fires — lockout alert |

---

## Investigation

- **Source IP:** 192.168.56.106 (Kali Linux attacker)
- **Target Account:** labuser
- **Protocol:** SMB port 445
- **Failed Attempts:** 5 before correct password found
- **Correct Password:** Summer2024! (found by CrackMapExec)
- **Tool Identified:** CrackMapExec via connection pattern

---

## Impact Assessment

- labuser account credentials compromised
- Attacker gained authenticated SMB access
- Risk of lateral movement using compromised credentials

---

## Containment Steps

1. Block attacker IP at firewall
   ```powershell
   netsh advfirewall firewall add rule name="Block-Attacker" dir=in action=block remoteip=192.168.56.106
   ```
2. Disable compromised account
   ```powershell
   Disable-LocalUser -Name "labuser"
   ```
3. Reset all credentials on affected host
4. Review SMB exposure — disable if not needed

---

## Lessons Learned

- ✅ Alert fired within 3 minutes of attack start
- ✅ Account lockout policy working correctly
- Recommendation: Reduce lockout threshold from 10 to 3 attempts
- Recommendation: Disable SMBv1, restrict SMB to required hosts only
- Recommendation: Implement MFA for all administrative accounts
