# 🛡️ SOC & Detection Engineering Home Lab

A comprehensive, custom-built Active Directory environment designed to simulate real-world cyber attacks and engineer robust detection rules. This project bridges the gap between offensive operations (Red Teaming) and defensive monitoring (Blue Teaming/SOC) using industry-standard tools like Splunk, Sysmon, and Kali Linux.

## 🏗️ Architecture & Infrastructure

The lab is distributed across two high-performance physical machines, isolating the attack infrastructure from the target network.

### Hardware & Virtualization
* **Defensive / Target Host (The "Corporate Network"):**
    * **Hardware:** Custom PC build (Intel Core i7-13700K, 32GB RAM 6400MHz DDR5).
    * **Hypervisor:** VMware Workstation.
* **Offensive Host (The "Threat Actor"):**
    * **Hardware:** Apple MacBook Pro M1.
    * **Hypervisor:** VMware Fusion Pro.

### Virtual Machines & Roles
| Machine Name | OS | Role | Telemetry / Agents |
| :--- | :--- | :--- | :--- |
| **Splunk-SIEM** | Ubuntu Server | Centralized SIEM / Indexer & Search Head | Splunk Enterprise (Port 8000, 9997) |
| **DC-01** | Windows Server 2022 | Domain Controller (`soclab.local`) | Sysmon (SwiftOnSecurity), Splunk UF |
| **WIN10-CLIENT** | Windows 10 Pro | Domain-joined Workstation | Sysmon (SwiftOnSecurity), Splunk UF |
| **Kali-Attacker** | Kali Linux (ARM64) | Red Team Operations / Attack Origin | Nmap, NetExec, Hydra, Metasploit |

## 🛠️ Technologies & Tools

* **Log Management & SIEM:** Splunk Enterprise, Splunk Universal Forwarder
* **Endpoint Telemetry:** Microsoft Sysmon (System Monitor) with SwiftOnSecurity configuration
* **Operating Systems:** Windows Server 2022 (AD DS), Windows 10, Ubuntu Linux, Kali Linux
* **Offensive Security:** Nmap, NetExec (CrackMapExec), Hydra
* **Networking:** Bridged internal networks, Windows Defender Firewall (managed via GPO/CMD for testing)

---

## ⚙️ Installation & Setup (Data Pipeline)

The core data pipeline is designed to forward endpoint activity to the SIEM in near real-time.

1.  **Sysmon Deployment:** Installed on both Windows endpoints using a tuned configuration to reduce noise while capturing critical process creations, network connections, and file modifications.
2.  **Splunk Universal Forwarder (UF):** Configured to monitor local Event Logs (Security, System, Application, Directory Services).
3.  **Manual Input Configuration:** Sysmon logs were manually routed to the UF by editing the `inputs.conf` file:
    ```ini
    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = false
    renderXml = true
    
    [WinEventLog://Security]
    disabled = false
    ```
4.  **Parsing & Normalization:** Splunk Add-on for Microsoft Windows and Sysmon were installed on the Ubuntu Search Head to parse XML logs into actionable fields.

---

## ⚔️ Attack & Detection Scenarios

### Scenario 1: Network Reconnaissance (Stealth Scan)
* **Objective:** Identify active hosts and open ports on the target workstation.
* **Attack Vector:** Aggressive Nmap scan executed from the Kali MacBook.
    ```bash
    sudo nmap -sC -sV -A <WIN10_IP>
    ```
* **Detection (Splunk):** Leveraging Sysmon Event ID 3 (Network Connection) to detect abnormal spikes in port scanning activity from a single source.
    ```spl
    index=main source="*Sysmon*" EventCode=3 host="WIN10-CLIENT"
    | stats count by SourceIp, DestinationPort 
    | sort - count
    ```

### Scenario 2: SMB Brute-Force Attack
* **Objective:** Gain unauthorized access to a domain user account (`jkowalski`) via SMB.
* **Attack Vector:** Dictionary attack using NetExec / Hydra.
    ```bash
    crackmapexec smb <WIN10_IP> -u jkowalski -p /usr/share/wordlists/fasttrack.txt
    ```
* **Detection Engineering (Overcoming Localization Issues):** During the analysis of **Event ID 4625 (Logon Failure)**, a critical real-world engineering challenge was encountered. The target Windows OS was localized in Polish. Consequently, standard Splunk parsers failed to extract fields like `TargetUserName` or `IpAddress`. 
    
    *The solution:* Analyzing the raw XML logs revealed the localized field names, which were then used to build the final detection query:
    ```spl
    index=main EventCode=4625
    | stats count by Nazwa_konta, "Adres źródłowy sieci", ComputerName
    | sort - count
    ```
    *Result:* Successfully identified the attacker's IP (`192.168.10.22`) and the compromised account with over 250 failed attempts in a matter of seconds.

---

## 🚀 Future Roadmap & Next Steps

This lab is a living project. Upcoming scenarios include:

- [x] Initial Infrastructure Setup & Data Pipeline
- [x] Nmap Recon & Sysmon Network Detetion
- [x] SMB Brute Force Detection
- [ ] Automated Alerting (Creating Dashboards for Logon Anomalies)
- [ ] Pass-the-Hash (PtH) attacks and Event ID 4624 (Logon Type 9) analysis
- [ ] Advanced Lateral Movement using C2 Frameworks (e.g., HexStrike) and detecting Process Injection (Sysmon Event ID 10)
- [ ] Implementing Sigma Rules

---
*Disclaimer: This environment is completely isolated and built solely for educational purposes and detection engineering research.*
