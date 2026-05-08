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

## Attacker Perspective (Kali Linux Evidence)

<img width="829" height="406" alt="hash-admin-pass" src="https://github.com/user-attachments/assets/245cb82d-850c-43f4-9fce-9543a92157cc" />


---
