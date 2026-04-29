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

## 📂 Repository Structure

This repository is divided into distinct operational phases:

* 📁 **[Infrastructure/](./Infrastructure)** - Deployment scripts, agent configurations, and data pipeline setup.
* 📁 **[Offensive/](./Offensive)** - Attack playbooks, payload generation, and execution logs.
* 📁 **[Defensive/](./Defensive)** - Threat hunting SPL queries, blind spot analysis, and detection logic.

## 🚀 Future Roadmap & Next Steps

This lab is a living project. Upcoming scenarios include:

- [x] Initial Infrastructure Setup & Data Pipeline
- [x] Network Recon & Documentation of Detection Blind Spots
- [x] SMB Brute Force Execution & Detection (Overcoming Localization Issues)
- [ ] Pass-the-Hash (PtH) attacks and Event ID 4624 (Logon Type 9) analysis
- [ ] Advanced Lateral Movement using C2 Frameworks (e.g., HexStrike) 
- [ ] Detecting Process Injection (Sysmon Event ID 10)
- [ ] Implementing automated alerts and Sigma Rules

---
*Disclaimer: This environment is completely isolated and built solely for educational purposes and detection engineering research.*
