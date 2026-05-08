# 🛡️ Detection Analysis: Pass-the-Hash (PtH) & Credential Dumping

This analysis focuses on identifying signs of credential theft and the subsequent use of those credentials via Pass-the-Hash (PtH).

## 1. Detection Engineering: The "Logon Type 9" Myth
A common misconception in SOC environments is that Pass-the-Hash always generates **Event ID 4624, Logon Type 9 (NewCredentials)**. 

However, Type 9 is only logged on the *source* machine if the attacker is using a Windows host (e.g., Mimikatz). Because this attack originated from a **Linux machine (Kali)**, the target Windows host interprets the incoming hash simply as standard NTLM network authentication.

## 2. Correct Detection Logic (Target Machine perspective)
To accurately detect this variant of PtH originating from non-Windows devices, analysts must look for:
* **Event ID:** 4624 (Successful Logon)
* **Logon Type:** 3 (Network)
* **Authentication Package:** NTLM (instead of Kerberos)
* **Source IP:** An anomalous or non-administrative IP address (in this case, the Kali Linux machine: `192.168.10.28`).

### Threat Hunting SPL Query
```spl
index=main source="*Security*" EventCode=4624 "192.168.10.28" Administrator
| table _time, host, "Nazwa_konta", "Źródłowy adres sieciowy", "Typ_logowania", "Pakiet_uwierzytelniania"
| sort - _time
```

---
### Defender Perspective (Splunk Detection Evidence)
<img width="2552" height="1001" alt="image" src="https://github.com/user-attachments/assets/7ef877ad-fa39-4480-a08c-568a39865fa2" />
