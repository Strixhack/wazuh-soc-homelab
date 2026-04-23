# MITRE ATT\&CK Coverage Matrix

## Wazuh SOC Home Lab — Attack Coverage

|#|Attack Name|Tactic|Technique ID|Sub-Technique|Wazuh Rule|Level|Detected|
|-|-|-|-|-|-|-|-|
|1|SMB Brute Force|Credential Access|T1110|T1110.002 — Password Cracking|60204|10|✅|
|2|Mimikatz Credential Dump|Credential Access|T1003|T1003.001 — LSASS Memory|100002|14|✅|
|3|Reverse Shell / C2|Execution|T1059|T1059.001 — PowerShell|100003|12|✅|
|4|PowerShell Obfuscation|Defense Evasion|T1027|T1027 — Obfuscated Files|100004|12|✅|
|5|Privilege Escalation|Privilege Escalation|T1548|T1548.002 — Bypass UAC|100006|12|✅|
|6|Registry Persistence|Persistence|T1547|T1547.001 — Registry Run Keys|92302|6|✅|
|7|Network Port Scanning|Discovery|T1046|T1046 — Network Service Scanning|100008|10|✅|
|8|File Integrity Violation|Impact|T1565|T1565.001 — Stored Data Manipulation|550/554|7|✅|
|9|Local Account Discovery|Discovery|T1087|T1087.001 — Local Account|100009|8|✅|
|10|Event Log Clearing|Defense Evasion|T1070|T1070.001 — Clear Windows Event Logs|100010|14|✅|

\---

## Coverage by Tactic

|Tactic|Techniques Covered|Count|
|-|-|-|
|Credential Access|T1110.002, T1003.001|2|
|Defense Evasion|T1027, T1070.001|2|
|Discovery|T1046, T1087.001|2|
|Execution|T1059.001|1|
|Persistence|T1547.001|1|
|Privilege Escalation|T1548.002|1|
|Impact|T1565.001|1|
|**Total**|**8 Tactics**|**10**|

\---

## Detection Summary

|Severity|Count|Attacks|
|-|-|-|
|🔴 Critical (Level 12-15)|6|Mimikatz, Reverse Shell, PowerShell, Privesc, Registry, Log Clearing|
|🟠 High (Level 8-11)|2|SMB Brute Force, FIM Violation|
|🟡 Medium (Level 5-7)|1|Port Scanning|
|🟢 Low (Level 1-4)|1|Account Discovery|
|**Total**|**10**|**100% Detection Rate**|



