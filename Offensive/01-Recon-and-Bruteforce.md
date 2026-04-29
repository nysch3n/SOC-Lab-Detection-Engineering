# ⚔️ Attack Scenario 01: Recon & SMB Brute-Force

This document covers the initial access attempts against the Windows 10 domain-joined client.

## Phase 1: Network Reconnaissance (Stealth Scan)
* **Objective:** Identify active hosts and open ports on the target workstation.
* **Execution:** Aggressive Nmap scan executed from the Kali MacBook.

      sudo nmap -sC -sV -A <WIN10_IP>

* **Red Team Notes:** The scan successfully enumerated open ports (including 135, 445 for SMB, and 3389). However, modern Windows Defender Firewall profiles initially dropped ICMP requests.

## Phase 2: SMB Brute-Force Attack
* **Objective:** Gain unauthorized access to a domain user account (`jkowalski`) via SMB.

### Attempt 1: Hydra (Failed)
Initially, `hydra` was used to attempt the brute-force:

    hydra -l jkowalski -P /usr/share/wordlists/fasttrack.txt smb://<WIN10_IP>

* **Failure Reason:** Hydra returned an `invalid reply from target` error. This is a common issue when attacking modern Windows systems (Win 10 / Server 2022) due to strict SMBv2/v3 protocol negotiation and the deprecation of SMBv1.

### Attempt 2: NetExec / CrackMapExec (Success)
Pivoted to `NetExec` (formerly CrackMapExec), which natively handles modern Windows authentication protocols.

    crackmapexec smb <WIN10_IP> -u jkowalski -p /usr/share/wordlists/fasttrack.txt

* **Result:** Successfully bombarded the endpoint with authentication attempts, resulting in an eventual password match.

<img width="1336" height="855" alt="NetExec Execution" src="https://github.com/user-attachments/assets/21a3bd34-1b76-45ce-a190-9a80f02acfc6" />
