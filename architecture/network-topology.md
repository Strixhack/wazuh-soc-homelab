# Lab Architecture — Wazuh SOC Home Lab

## Network Topology

```
┌─────────────────────────────────────────────────────────┐
│                   HOST MACHINE (16GB RAM)               │
│                                                         │
│  ┌─────────────────────┐   ┌─────────────────────────┐ │
│  │  Wazuh All-in-One   │   │    Windows 10 VM        │ │
│  │  OVA — 4GB RAM      │   │    4GB RAM              │ │
│  │                     │   │                         │ │
│  │  - Wazuh Manager    │◄──│  - Wazuh Agent          │ │
│  │  - Wazuh Indexer    │   │  - Sysmon               │ │
│  │  - Wazuh Dashboard  │   │  - PowerShell Logging   │ │
│  │                     │   │  - FIM Enabled          │ │
│  │  192.168.56.101     │   │  192.168.56.105         │ │
│  └─────────────────────┘   └─────────────────────────┘ │
│                                      ▲                  │
│  ┌─────────────────────┐             │ attacks          │
│  │  Kali Linux VM      │─────────────┘                  │
│  │  2GB RAM            │                                │
│  │                     │                                │
│  │  - CrackMapExec     │                                │
│  │  - Mimikatz         │                                │
│  │  - Metasploit       │                                │
│  │  - Nmap             │                                │
│  │  192.168.56.106     │                                │
│  └─────────────────────┘                               │
└─────────────────────────────────────────────────────────┘
```

## Network Configuration

|VM|Adapter 1|Adapter 2|Purpose|
|-|-|-|-|
|Wazuh OVA|NAT (10.0.2.x)|Host-Only (192.168.56.101)|SIEM Manager|
|Windows 10|NAT (10.0.2.x)|Host-Only (192.168.56.105)|Monitored Agent|
|Kali Linux|NAT (10.0.2.x)|Host-Only (192.168.56.106)|Attacker|

**Promiscuous Mode:** Allow All on all Host-Only adapters

## Component Details

### Wazuh All-in-One OVA

* **Role:** SIEM Manager + Indexer + Dashboard
* **RAM:** 4GB
* **Key Files:**

  * `/var/ossec/etc/ossec.conf` — Manager config
  * `/var/ossec/etc/rules/local\_rules.xml` — Custom rules
  * `/var/ossec/logs/alerts/alerts.log` — Live alerts

### Windows 10 VM

* **Role:** Monitored endpoint (agent)
* **RAM:** 4GB
* **Key Components:**

  * Wazuh Agent — forwards logs to manager
  * Sysmon — rich telemetry (EID 1,3,10,11,13)
  * PowerShell Script Block Logging (EID 4104)
  * FIM — monitors C:\\Temp and C:\\SOC-Lab
* **Key Files:**

  * `C:\\Program Files (x86)\\ossec-agent\\ossec.conf`
  * `C:\\SOC-Lab\\sysmon-custom.xml`

### Kali Linux VM

* **Role:** Attacker machine
* **RAM:** 2GB
* **Tools:** CrackMapExec, Mimikatz, Metasploit, Nmap, impacket

## Telemetry Sources

|Source|Log Channel|Events|
|-|-|-|
|Sysmon|Microsoft-Windows-Sysmon/Operational|Process, Network, Registry, File|
|Windows Security|Security|Logon, Account, Policy|
|Windows System|System|Service, Driver events|
|PowerShell|Microsoft-Windows-PowerShell/Operational|Script blocks (EID 4104)|
|Windows Firewall|pfirewall.log|Network drops/allows|

## Data Flow

```
Windows VM Events
      │
      ▼ (port 1514 TCP)
Wazuh Agent
      │
      ▼
Wazuh Manager ──► Rules Engine ──► Alert Generated
      │                                    │
      ▼                                    ▼
Wazuh Indexer                    VirusTotal API
      │                          (hash enrichment)
      ▼
Wazuh Dashboard
(Analyst View)
```

