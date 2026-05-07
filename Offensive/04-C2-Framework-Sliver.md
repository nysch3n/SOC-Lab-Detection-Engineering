# ⚔️ Attack Scenario 04: Command & Control (C2) Operations

## 1. Objective
Deploy a Command and Control (C2) framework to establish a persistent, stealthy connection (beacon) to the target machine, simulating an Advanced Persistent Threat (APT) actor.

## 2. Execution & Delivery
Instead of relying on noisy scripts like Impacket, we utilized a modern C2 framework to interact with the target directly in memory.
* **C2 Framework:** Sliver (Open-source Go-based alternative to Cobalt Strike)
* **Payload:** HTTP Beacon (`update.exe`)
* **Delivery Method:** Hosted via a Python HTTP server on Kali Linux and executed on the target Windows machine.

## 3. Post-Exploitation (Operational Commands)
Once the beacon successfully phoned home and established a session, post-exploitation reconnaissance was conducted using Sliver's built-in commands. This technique is much stealthier than spawning `cmd.exe` or `powershell.exe`, reducing the footprint left in Windows Event Logs (e.g., avoiding Event ID 1 - Process Creation).

```bash
sliver > sessions
sliver > use [SESSION_ID]
sliver (SESSION_NAME) > info
```

### 📸 Attacker Perspective (Sliver Session & Host Recon)
<img width="1040" height="775" alt="sliver-console-c2" src="https://github.com/user-attachments/assets/17197351-2675-4397-a7dd-7b9ae032a5e0" />
