# 🛡️ Wazuh SOC Home Lab — Threat Detection \& Attack Simulation

> \*\*MS Cybersecurity Major Project | SRH Berlin University of Applied Sciences\*\*

A production-grade Security Operations Center (SOC) home lab built to simulate, detect, and respond to real-world cyber attacks using Wazuh SIEM/XDR, Sysmon, and the MITRE ATT\&CK framework.

\---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   HOST MACHINE (16GB RAM)               │
│                                                         │
│  ┌─────────────────────┐   ┌─────────────────────────┐ │
│  │  Wazuh All-in-One   │   │    Windows 10 VM        │ │
│  │  OVA — 4GB RAM      │   │    Agent + Target       │ │
│  │  Manager + Indexer  │   │    Sysmon + Wazuh Agent │ │
│  │  + Dashboard        │   │    192.168.56.105       │ │
│  │  192.168.56.101     │   └─────────────────────────┘ │
│  └─────────────────────┘                               │
│  ┌─────────────────────┐                               │
│  │  Kali Linux VM      │  ← Attacker machine           │
│  │  2GB RAM            │    192.168.56.106             │
│  └─────────────────────┘                               │
└─────────────────────────────────────────────────────────┘
         NAT (internet) + Host-Only (192.168.56.x)
```

|Component|Role|IP Address|
|-|-|-|
|Wazuh OVA|SIEM/XDR Manager + Indexer + Dashboard|192.168.56.101|
|Windows 10 VM|Monitored Endpoint — Sysmon + Wazuh Agent|192.168.56.105|
|Kali Linux VM|Attacker Machine — Attack simulations|192.168.56.106|

**Network:** Dual adapter — NAT (internet access) + Host-Only (isolated lab communication, promiscuous mode: Allow All)

\---

## ⚔️ 10 Attack Simulations — 100% Detection Rate

|#|Attack|MITRE ID|Tactic|Severity|Rule|Detected|
|-|-|-|-|-|-|-|
|1|SMB Brute Force|T1110.002|Credential Access|🟠 High|60204|✅|
|2|Mimikatz Credential Dump|T1003.001|Credential Access|🔴 Critical|100002|✅|
|3|Reverse Shell / C2|T1059.001|Execution|🔴 Critical|100003|✅|
|4|PowerShell Obfuscation|T1027|Defense Evasion|🔴 Critical|100004|✅|
|5|Privilege Escalation|T1548.002|Privilege Escalation|🔴 Critical|100006|✅|
|6|Registry Run Key Persistence|T1547.001|Persistence|🔴 Critical|92302|✅|
|7|Network Port Scanning|T1046|Discovery|🟡 Medium|100008|✅|
|8|File Integrity Violation|T1565.001|Impact|🟠 High|550/554|✅|
|9|Local Account Discovery|T1087.001|Discovery|🟢 Low|100009|✅|
|10|Event Log Clearing|T1070.001|Defense Evasion|🔴 Critical|100010|✅|

**Detection Rate: 10/10 — 100%**

\---

## 🛠️ Tools \& Technologies

|Category|Tool|Purpose|
|-|-|-|
|SIEM/XDR|Wazuh 4.7|Central detection and alerting platform|
|Endpoint Telemetry|Sysmon|Rich Windows process/network/registry events|
|Threat Intelligence|VirusTotal API|Automated IOC hash enrichment|
|Brute Force|CrackMapExec|SMB credential spraying|
|Credential Dumping|Mimikatz|LSASS memory credential extraction|
|C2 / Exploit|Metasploit / msfvenom|Reverse shell payload generation|
|Reconnaissance|Nmap|Network port and service scanning|
|Platform|VirtualBox|Lab hypervisor|

\---

## 📊 MITRE ATT\&CK Coverage

```
Credential Access     ██████████  T1110.002, T1003.001
Defense Evasion       ██████████  T1027, T1070.001
Execution             █████       T1059.001
Persistence           █████       T1547.001
Privilege Escalation  █████       T1548.002
Discovery             ██████████  T1046, T1087.001
Impact                █████       T1565.001
```

**8 Tactics | 10 Techniques | 100% Detection Rate**

\---

## 📁 Repository Structure

|Folder|Contents|
|-|-|
|`/architecture`|Lab network diagram and topology documentation|
|`/configs`|Wazuh ossec.conf, custom detection rules, Sysmon config|
|`/attack-simulations`|Step-by-step attack execution for all 10 attacks|
|`/ir-reports`|Full incident response report per attack scenario|
|`/mitre-coverage`|Complete MITRE ATT\&CK coverage matrix|
|`/screenshots`|Wazuh dashboard alert screenshots|

\---

## 🔧 Key Skills Demonstrated

* ✅ SIEM deployment and configuration (Wazuh)
* ✅ Custom detection rule engineering (12 custom rules)
* ✅ MITRE ATT\&CK framework mapping and coverage analysis
* ✅ Incident response documentation and runbooks
* ✅ Threat intelligence integration (VirusTotal API)
* ✅ Windows endpoint forensics (Sysmon, Event IDs)
* ✅ Attack simulation and detection validation
* ✅ Log analysis and alert triage

\---

## 🚀 Quick Start — Lab Setup

### Prerequisites

* VirtualBox 7.x
* 16GB RAM minimum
* Wazuh OVA (all-in-one)
* Windows 10 VM
* Kali Linux VM

### Network Configuration

```
Each VM:
  Adapter 1 → NAT           (internet access)
  Adapter 2 → Host-Only     (lab communication)
  Promiscuous Mode → Allow All
```

### Agent Installation

```powershell
# On Windows VM — PowerShell as Administrator
msiexec.exe /i wazuh-agent.msi WAZUH\_MANAGER="192.168.56.101" WAZUH\_AGENT\_NAME="Windows-SOC-Lab" /q
NET START WazuhSvc
```

\---

## 👤 Author

**Kundan Vidhate**
MS Cybersecurity — SRH Berlin University of Applied Sciences

[!\[LinkedIn](https://www.linkedin.com/in/kundan-vidhate-3479833b1 )](your-linkedin-url)

\---

## 📄 License

This project is for educational purposes only. All attack simulations were conducted in an isolated lab environment on systems owned and controlled by the author.

