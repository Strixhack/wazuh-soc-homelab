# IR-001 — SMB Brute Force Attack

**Date:** 2026-04-12 | **Severity:** HIGH | **Status:** Detected \& Contained
**MITRE:** T1110.002 | **Rule:** 60204 | **Level:** 10

## Detection

* Rule 60204 fired on multiple Windows logon failures (EID 4625)
* Account lockout triggered (EID 4740)

## Timeline

|Time|Event|
|-|-|
|T+00:00|Attacker 192.168.56.106 initiates SMB connections|
|T+00:01|Multiple STATUS\_LOGON\_FAILURE responses|
|T+00:03|Rule 60204 fires — brute force detected|
|T+00:05|Account lockout triggered|

## Investigation

* **Source IP:** 192.168.56.106 (Kali)
* **Target:** labuser via SMB port 445
* **Tool:** CrackMapExec identified via connection pattern

## Containment

1. Block attacker IP at firewall
2. Disable compromised account
3. Reset all credentials on affected host
4. Review SMB exposure

## Lessons Learned

* Alert fired within 3 minutes of attack ✅
* Recommendation: Reduce lockout threshold to 3 attempts
* Recommendation: Restrict SMB to required hosts only

\---

# IR-002 — Credential Dumping (Mimikatz)

**Date:** 2026-04-12 | **Severity:** CRITICAL | **Status:** Detected \& Contained
**MITRE:** T1003.001 | **Rule:** 100002 | **Level:** 14

## Detection

* Rule 100002 fired on Mimikatz process creation (Sysmon EID 1)
* SHA256 hash captured: 61C0810A23580CF492A6BA4F7654566108331E7A4134C968C2D6A05261B2D8A1

## Timeline

|Time|Event|
|-|-|
|T+00:00|mimikatz.exe spawned by powershell.exe (Sysmon EID 1)|
|T+00:01|privilege::debug — SeDebugPrivilege obtained|
|T+00:02|lsass.exe memory accessed (Sysmon EID 10)|
|T+00:02|Rule 100002 fires — Level 14 Critical|

## Investigation

* **Process:** C:\\SOC-Lab\\mimikatz\\x64\\mimikatz.exe
* **Parent:** powershell.exe
* **SHA256:** 61C0810A...D8A1 (68/72 VT engines flagged)
* **User:** DESKTOP-2F01H2Q\\strix

## Impact

* ALL cached credentials compromised
* NTLM hashes exposed — pass-the-hash possible
* Plaintext passwords potentially extracted

## Containment

1. Isolate host immediately
2. Reset ALL passwords for users logged in
3. Run klist purge to invalidate Kerberos tickets
4. Preserve memory dump for forensics

## Lessons Learned

* Alert fired within 2 seconds of LSASS access ✅
* Recommendation: Enable Windows Credential Guard
* Recommendation: Restrict SeDebugPrivilege

\---

# IR-003 — Reverse Shell / C2 Beacon

**Date:** 2026-04-13 | **Severity:** CRITICAL | **Status:** Detected \& Contained
**MITRE:** T1059.001 | **Rule:** 100003 | **Level:** 12

## Detection

* Rule 100003 fired on outbound TCP:4444 connection (Sysmon EID 3)

## Timeline

|Time|Event|
|-|-|
|T+00:00|update.exe downloaded via SMB (Sysmon EID 11)|
|T+00:01|update.exe executed (Sysmon EID 1)|
|T+00:02|Outbound TCP:4444 to 192.168.56.106 (Sysmon EID 3)|
|T+00:02|Rule 100003 fires|
|T+00:03|Meterpreter session opened on Kali|

## Investigation

* **Malicious File:** update.exe
* **C2 Server:** 192.168.56.106:4444
* **Protocol:** Meterpreter TCP

## Containment

1. Kill update.exe process tree
2. Block outbound port 4444
3. Delete malicious binary
4. Isolate host from network

## Lessons Learned

* Sysmon EID 3 caught C2 beacon immediately ✅
* Recommendation: Block all non-standard outbound ports
* Recommendation: Implement application whitelisting

\---

# IR-004 — Malicious PowerShell Execution

**Date:** 2026-04-13 | **Severity:** CRITICAL | **Status:** Detected \& Contained
**MITRE:** T1027 | **Rules:** 100004, 100005 | **Level:** 12

## Detection

* Rule 100004 on encoded command (-EncodedCommand flag)
* Rule 100005 on IEX download cradle
* EID 4104 script block logging captured decoded content

## Containment

1. Enable PowerShell Constrained Language Mode
2. Block encoded command execution via policy
3. Review and block remote script URL

## Lessons Learned

* Script block logging caught fileless attack ✅
* Recommendation: Enable AMSI integration
* Recommendation: Implement Just Enough Administration

\---

# IR-005 — Privilege Escalation via Scheduled Task

**Date:** 2026-04-13 | **Severity:** CRITICAL | **Status:** Detected \& Contained
**MITRE:** T1548.002 | **Rule:** 100006 | **Level:** 12

## Detection

* Rule 100006 on schtasks /create /ru SYSTEM
* EID 4698 scheduled task creation logged

## Investigation

* **Task Name:** WindowsUpdate
* **Run As:** NT AUTHORITY\\SYSTEM
* **Output:** nt authority\\system confirmed in output.txt

## Containment

1. Delete malicious scheduled task
2. Audit all scheduled tasks for persistence
3. Restrict schtasks.exe via AppLocker

## Lessons Learned

* SYSTEM task creation detected immediately ✅
* Recommendation: Least privilege enforcement

\---

# IR-006 — Registry Run Key Persistence

**Date:** 2026-04-20 | **Severity:** CRITICAL | **Status:** Detected \& Remediated
**MITRE:** T1547.001 | **Rule:** 92302 | **Level:** 6

## Detection

* Rule 92302 fired on reg.exe modifying CurrentVersion\\Run
* Sysmon EID 13 captured registry value set event

## Investigation

* **Registry Key:** HKCU...\\CurrentVersion\\Run\\SecurityUpdate
* **Value:** C:\\SOC-Lab\\update.exe
* **Image:** C:\\Windows\\system32\\reg.exe

## Containment

1. Delete malicious registry key
2. Audit all Run/RunOnce keys
3. Investigate update.exe binary with VirusTotal

## Lessons Learned

* Sysmon EID 13 caught modification in real time ✅
* Recommendation: Monitor all autorun registry locations

\---

# IR-007 — Network Reconnaissance / Port Scan

**Date:** 2026-04-20 | **Severity:** MEDIUM | **Status:** Detected
**MITRE:** T1046 | **Rule:** 100008 | **Level:** 10

## Detection

* Nmap SYN scan detected from 192.168.56.106
* Multiple inbound connection attempts on various ports

## Investigation

* **Source:** 192.168.56.106 (Kali attacker)
* **Scan Types:** SYN, Aggressive, Full port scan
* **Tool:** Nmap (identified via packet pattern)

## Containment

1. Block scanning IP at firewall
2. Review and close unnecessary open ports
3. Treat scan as precursor to attack

## Lessons Learned

* Reconnaissance detected before actual attack ✅
* Recommendation: Minimize attack surface

\---

# IR-008 — File Integrity Violation

**Date:** 2026-04-20 | **Severity:** HIGH | **Status:** Detected \& Investigated
**MITRE:** T1565.001 | **Rules:** 550, 554 | **Level:** 7

## Detection

* Rule 554 on new file creation in monitored directory
* Rule 550 on file modification — hash mismatch detected

## Investigation

* **Modified File:** C:\\Temp\\config.txt
* **Detection Method:** SHA256 hash comparison
* **Old Hash vs New Hash:** Mismatch confirmed tampering

## Containment

1. Restore file from clean backup
2. Investigate who modified the file
3. Review access permissions on sensitive paths

## Lessons Learned

* FIM detected tampering within seconds ✅
* Recommendation: Read-only permissions on config files

\---

# IR-009 — Local Account Discovery

**Date:** 2026-04-20 | **Severity:** LOW | **Status:** Detected
**MITRE:** T1087.001 | **Rule:** 100009 | **Level:** 8

## Detection

* Rule 100009 fired on net user and Get-LocalUser commands
* Remote SMB enumeration from Kali also detected

## Investigation

* **Commands:** net user, net localgroup, Get-LocalUser
* **Remote Source:** 192.168.56.106 via CrackMapExec
* **Data Exposed:** Full user list and group memberships

## Containment

1. Disable compromised account used for remote enumeration
2. Restrict net.exe usage via AppLocker

## Lessons Learned

* Enumeration activity detected correctly ✅
* Recommendation: Principle of least privilege

\---

# IR-010 — Event Log Clearing

**Date:** 2026-04-20 | **Severity:** CRITICAL | **Status:** Detected — Active Incident
**MITRE:** T1070.001 | **Rules:** 100010, 100011 | **Level:** 14

## Detection

* Rule 100010 fired on wevtutil cl pattern
* EID 1102 (Security log cleared) generated
* EID 104 (System log cleared) generated

## Timeline

|Time|Event|
|-|-|
|T+00:00|wevtutil.exe executed (Sysmon EID 1)|
|T+00:01|Security log cleared — EID 1102|
|T+00:01|Rule 100010 fires — Level 14 Critical|
|T+00:02|System + App logs cleared|
|T+00:03|All local logs destroyed — SIEM copy intact|

## Investigation

* **Tool:** wevtutil.exe (built-in Windows)
* **Logs Cleared:** Security, System, Application, PowerShell
* **Intent:** Destroy forensic evidence after attack

## Key Finding

All logs were already forwarded to Wazuh SIEM and remained intact despite local clearing — proving the critical value of centralized log management.

## Containment

1. Isolate host IMMEDIATELY
2. Preserve memory dump before shutdown
3. Treat as confirmed breach — full IR response
4. Begin forensic investigation using SIEM logs

## Lessons Learned

* Wazuh retained all forwarded logs — evidence safe ✅
* This proves SIEM is last line of forensic defense ✅
* Recommendation: Forward all logs to SIEM in real time
* Recommendation: Restrict wevtutil.exe via AppLocker

