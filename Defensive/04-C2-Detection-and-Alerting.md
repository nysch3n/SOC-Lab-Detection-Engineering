# 🛡️ Detection Engineering: C2 Beaconing & Automated Alerting

## 1. Scenario Overview
During an advanced post-exploitation simulation using the **Sliver C2 Framework**, the target Windows 10 machine established a Command & Control beacon. 

Instead of relying on manual Threat Hunting, the objective was to engineer an automated detection rule within Splunk to alert the SOC team of unauthorized external connections from suspicious processes.

## 2. Threat Hunting (Sysmon Event ID 3)
To identify the C2 beaconing activity, we analyzed Network Connections originating from the target host. The Sliver HTTP beacon was detected phoning home to the Kali Linux IP.

**Detection Query (SPL):**
```spl
index=main source="*Sysmon*" EventCode=3 DestinationIp="192.168.10.28"
| table _time, host, Image, SourceIp, DestinationIp, DestinationPort
| sort - _time
```

### Threat Hunt Evidence

<img width="2525" height="1227" alt="splunk-c2-hunt" src="https://github.com/user-attachments/assets/41afa1f2-9eb6-4516-b93a-dabf2c45f46f" />


---

## 3. Automated SOC Alert Configuration
To transition from manual hunting to automated detection, a scheduled alert was engineered in Splunk to monitor for continuous beaconing behavior.

**Alert Parameters:**
* **Name:** `SOC ALERT: Suspicious C2 Network Connection (Beaconing)`
* **Schedule:** Cron-based (Every 5 minutes)
* **Trigger Condition:** Number of Results > 0
* **Action:** Add to Triggered Alerts
* **Severity:** High / Critical

### Detection Rule Deployment

<img width="2555" height="872" alt="splunk-c2-alert" src="https://github.com/user-attachments/assets/4099f8e0-80ec-45d5-891a-01f47d4ff4d9" />


This effectively closes the loop between Red Team execution (C2 deployment) and Blue Team automation (Detection Engineering).
