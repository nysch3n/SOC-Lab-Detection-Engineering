'''
# ⚔️ Attack Scenario 03: Credential Dumping & Pass-the-Hash (PtH)

## Phase 1: Credential Access (Dumping SAM/LSA)

- **Objective:** Extract NTLM hashes from the target machine's registry/memory to escalate privileges  
- **Execution:** Used Impacket's `secretsdump.py` to remotely dump credentials  
- **Defense Evasion Note:**  
  Windows Defender heavily monitors:
  - Registry hive access (SAM/SYSTEM)  
  - LSASS memory access  

  During execution:
  - AV detection was triggered  
  - Temporary files were created (linked to `MsMpEng.exe` activity)  
  - Temporary services were spawned (**Event ID 7045**)  

---

## Phase 2: Pass-the-Hash (Privilege Escalation)

- **Objective:**  
  Utilize the extracted NTLM hash of an administrative account to gain full system access without knowing the plaintext password  

- **Execution:**

    ```bash
    impacket-wmiexec -hashes <HASH> target_account@<TARGET_IP>
    ```

- **Result:**  
  Successful remote **SYSTEM-level access via WMI**, bypassing traditional password-based authentication  

---

## 📸 Attacker Perspective (Kali Linux Evidence)

![Pass-the-Hash Evidence](https://github.com/user-attachments/assets/8b8db2c3-c84f-4675-a02)
