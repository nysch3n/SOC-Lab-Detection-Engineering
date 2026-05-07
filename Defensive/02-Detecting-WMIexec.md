# 🛡️ Detection Analysis: WMI-Based Lateral Movement

This analysis focuses on detecting **fileless lateral movement** that bypasses traditional Anti-Virus (AV) signatures.

---

## 1. 🔍 Detection Gap

### ❌ Traditional AV (e.g., Defender)
- Failed to alert on the WMI connection  
- No malicious file was written to disk  
- Activity blended with legitimate Windows Management traffic  

### ✅ EDR / SIEM Approach
Detection is possible by monitoring:
- **Process Lineage**
- **Behavioral Anomalies**
- Using **Sysmon telemetry**

---

## 2. 🧬 Detection Logic (Process Ancestry)

The `wmiexec` tradecraft produces a distinctive process tree:

1. `WmiPrvSE.exe` (WMI Provider Host) acts as the **parent process**
2. It unexpectedly spawns:
   - `cmd.exe` *(or sometimes `powershell.exe`)*
3. The command line often contains:
   - `/Q /c` → used by Impacket to execute commands and redirect output  

---

## 3. 🕵️ Threat Hunting Query (Splunk SPL)

```spl
index=main source="*Sysmon*" EventCode=1 ParentImage="*\\WmiPrvSE.exe" Image="*\\cmd.exe"
| table _time, host, User, ParentImage, Image, CommandLine
| sort - _time
```

---

## 4. 📊 Analysis of Findings

The logs captured by Splunk clearly show attacker activity executed via WMI:

- Commands are run through `WmiPrvSE.exe`
- SOC analysts gain visibility into attacker behavior
- Example observed commands:
  - `whoami`
  - system discovery commands

➡️ Even though AV remained silent, **process-level telemetry exposed the attack**

---

## 5. 📸 Detection Evidence (Splunk)

<img width="2553" height="1237" alt="image" src="https://github.com/user-attachments/assets/a45c6ddd-fe12-40eb-ae67-7cb4d634d0c9" />


---

## 6. 💡 Engineering Takeaways

To improve detection and prevention:

### 🔎 Detection
- Monitor for:
  - `cmd.exe` spawned by `WmiPrvSE.exe`
  - `powershell.exe` spawned by `WmiPrvSE.exe`

### 🔐 Access Control
- Restrict **remote WMI access** to authorized admin accounts only  

### 🧑‍💻 Least Privilege
- Enforce least privilege policies  
- Prevent accounts (e.g., `jkowalski`) from having unnecessary local admin rights  

---

## ✅ Summary

| Area        | Insight |
|------------|--------|
| Detection Gap | AV misses fileless WMI execution |
| Key Indicator | `WmiPrvSE.exe → cmd.exe` |
| Visibility Source | Sysmon + SIEM |
| Defense Strategy | Monitoring + Access Control + Least Privilege |

---
