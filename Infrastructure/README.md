# ⚙️ Infrastructure & Data Pipeline Setup

The core data pipeline is designed to forward endpoint activity to the SIEM in near real-time.

## 1. Endpoint Telemetry Setup (Sysmon)
Sysmon was installed on both Windows endpoints (`WIN10-CLIENT` and `DC-01`) using the tuned [SwiftOnSecurity configuration](https://github.com/SwiftOnSecurity/sysmon-config). This reduces noise while capturing critical process creations, network connections, and file modifications.

**Installation Command:**
    sysmon.exe -i sysmonconfig-export.xml -accepteula

## 2. Log Forwarding (Splunk Universal Forwarder)
Configured to monitor local Event Logs (Security, System, Application, Directory Services). Sysmon logs were manually routed to the UF by editing the `inputs.conf` file.

**Configuration File Path:** `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`

**Configuration Content:**
    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = false
    renderXml = true
    
    [WinEventLog://Security]
    disabled = false

*Note: Service requires a restart (`net stop SplunkForwarder && net start SplunkForwarder`) after modifying this file.*

## 3. Parsing & Normalization
Splunk Add-on for Microsoft Windows and Sysmon were installed on the Ubuntu Search Head to parse XML logs into actionable fields.
