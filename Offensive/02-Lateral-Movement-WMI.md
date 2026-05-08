# ⚔️ Attack Scenario 02: Lateral Movement via WMI

This phase demonstrates how an attacker moves laterally within a network after obtaining valid credentials, specifically bypassing signature-based defenses like Windows Defender.

## Phase 1: Bypassing Windows Defender
* **Observation:** Initial attempts to use `impacket-psexec` were immediately blocked and quarantined by Windows Defender. This is because `psexec` drops a physical executable (`PSEXESVC.exe`) onto the `ADMIN$` share, which is a known signature.
* **Solution:** Pivoted to a **"Fileless"** technique using **WMI (Windows Management Instrumentation)**. 

## Phase 2: Execution (WMIexec)
* **Tool:** Impacket `wmiexec.py`
* **Objective:** Execute remote commands without dropping a binary to disk, staying resident only in memory and standard system processes.

**Command Executed from Kali:**
    impacket-wmiexec jkowalski:'Hacker123!'@<WIN10_IP>

**Result:**
Successfully obtained a semi-interactive shell. Executed discovery commands:
* `whoami`
* `systeminfo`
* `net user`

---
### Screenshot: Kali Linux Evidence
<img width="726" height="353" alt="kali-wmi" src="https://github.com/user-attachments/assets/7b26c943-bfee-444d-9c1b-471dd5a57527" />

---
