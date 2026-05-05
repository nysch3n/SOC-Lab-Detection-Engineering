# ⚔️ Attack Scenario 03: Credential Dumping & Pass-the-Hash (PtH)

## Phase 1: Credential Access (Dumping SAM/LSA)
* **Objective:** Extract NTLM hashes from the target machine's registry/memory to escalate privileges.
* **Execution:** Used Impacket's `secretsdump.py` to remotely dump credentials.
* **Defense Evasion Note:** Windows Defender heavily monitors registry saves (SAM/SYSTEM) and LSASS access. The tool's activity was detected by AV, creating temp files (due to `MsMpEng.exe` reaction) and spawning temporary services (Event ID 7045).

## Phase 2: Pass-the-Hash (Privilege Escalation)
* **Objective:** Utilize the extracted NTLM hash of an administrative account to gain full system control without knowing the plaintext password.
* **Execution:** 
  ```bash
  impacket-wmiexec -hashes <HASH> target_account@<TARGET_IP>
  
```
* **Result:** Successful remote SYSTEM-level access via WMI, bypassing traditional password requirements.

---
### 📸 Attacker Perspective (Kali Linux Evidence)
<img width="829" height="406" alt="hash-admin-pass" src="https://github.com/user-attachments/assets/8b8db2c3-c84f-4675-a02b-436d7df62056" />
