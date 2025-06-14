# SIEM Internship Program – Phase 1: Cyber Defense Lab
Phase 1 of the SIEM Internship Program. This project establishes foundational skills in Security Information and Event Management (SIEM) by building a detection lab, simulating attacks, and using Elastic Stack to identify threats.


## 🛠️ Lab Components

| Component         | Description                                         |
|------------------|------------------------------------------------------|
| VirtualBox        | Virtualization platform for running VMs             |
| Ubuntu Linux      | Attacker machine (Linux)                            |
| Windows 10        | Target machine for attack simulation                |
| Sysmon            | Logs detailed Windows activities                    |
| Splunk Enterprise | SIEM used for log ingestion and analysis            |
| Splunk Forwarder  | Sends logs from Windows 10 to Splunk on Ubuntu Linux |

---

## 🧱 Splunk Server Setup (Host Machine)

1. Download and install **Splunk Enterprise** from [Splunk Downloads](https://www.splunk.com/en_us/download.html).
2. Start the Splunk service and log in via `http://localhost:8000`.
3. Create a new index (e.g., `lab_index`) for log segregation.

---

## 🪟 Windows VM Setup

## 🛠 Install and Start Sysmon on Windows

    Download Sysmon from:
    https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

    Download Sysmon config (recommended):
    https://github.com/SwiftOnSecurity/sysmon-config

    Install command (in Command Prompt as Administrator):

### 1. Install Splunk Universal Forwarder

Download from [Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html).

```powershell
# Example install command (PowerShell)
Start-Process -Wait -FilePath "splunkforwarder-<version>-x64-release.msi"
```

### 2. Install and Configure Sysmon

1. Download Sysinternals Sysmon: [https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

```cmd
sysmon.exe -accepteula -i sysmonconfig.xml
```

> `sysmonconfig.xml` can be a custom configuration file or use [SwiftOnSecurity's Sysmon config](https://github.com/SwiftOnSecurity/sysmon-config)

### 3. Configure Splunk Forwarder

Edit `inputs.conf` and `outputs.conf` in:
`C:\Program Files\SplunkUniversalForwarder\etc\system\local\`

#### `inputs.conf`

```conf
[default]
host = windows-vm

[WinEventLog:Security]
disabled = 0

[WinEventLog:System]
disabled = 0

[WinEventLog:Application]
disabled = 0

[monitor://C:\Windows\System32\winevt\Logs]
disabled = false
index = lab_index
```

#### `outputs.conf`

```conf
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = <SPLUNK_SERVER_IP>:9997

[tcpout-server://<SPLUNK_SERVER_IP>:9997]
```

---

## 🐧 Ubuntu VM Setup

### 1. Install Splunk Universal Forwarder

```bash
wget -O splunkforwarder.tgz 'https://download.splunk.com/products/universalforwarder/releases/X.X.X/linux/splunkforwarder-X.X.X-Linux-x86_64.tgz'
tar -xvzf splunkforwarder.tgz
cd splunkforwarder
./bin/splunk start --accept-license
```

### 2. Enable Audit Logs (Auditd)

```bash
sudo apt install auditd audispd-plugins
sudo systemctl enable auditd
sudo systemctl start auditd
```

### 3. Configure `inputs.conf` and `outputs.conf`

Edit the files under:
`/opt/splunkforwarder/etc/system/local/`

#### `inputs.conf`

```conf
[default]
host = ubuntu-vm

[monitor:///var/log]
disabled = false
index = lab_index
```

To forward Audit logs specifically:

```conf
[monitor:///var/log/audit/audit.log]
disabled = false
index = lab_index
sourcetype = linux_audit
```

#### `outputs.conf`

```conf
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = <SPLUNK_SERVER_IP>:9997

[tcpout-server://<SPLUNK_SERVER_IP>:9997]
```

---

## 🛠 Useful Splunk Forwarder Commands

```bash
# Check monitored files
./splunk list monitor

# Add new monitor
./splunk add monitor /var/log/syslog

# Restart the forwarder
./splunk restart
```

---

## 🧼 Clean Events (on Splunk Server)

```bash
# Remove all events from index (use cautiously)
$SPLUNK_HOME/bin/splunk clean eventdata -index lab_index

# Restart Splunk
$SPLUNK_HOME/bin/splunk restart
```

---

## ✅ Verification

* Login to Splunk Enterprise.
* Go to **Search & Reporting** app.
* Run:

```spl
index=lab_index | stats count by host, sourcetype
```

* You should see logs from both Windows and Ubuntu VMs.

---
## 📁 Repo Structure

```cyber-intern-phase-1/
├── Folder
    ├── logs/ # Contains exported logs (JSON, TXT, etc.)
    ├── screenshots/ # Screenshots of setup, configurations, dashboards
    ├── reports/ # Summary notes, setup steps, attack findings
    ├── hints/ # Helpful links, commands, tips, cheat sheets
└── README.md # Project overview and setup guide
```
---

## ✅ Setup Checklist

| Item                                      | Status   |
|-------------------------------------------|----------|
| VirtualBox/VMware Installed               | ✅       |
| ubuntu Linux VM ready                     | ✅       |
| Windows 10 VM ready                       | ✅       |
| Sysmon                                    | ✅       |
| Splunk Enterprise & Forwarder setup complete, Sysmon installed | ✅       |
| GitHub repo created                       | ✅       |

---

## 🙋 Reflection Questions
Here are my thoughts and experiences based on building and working with the SIEM lab during Phase 1 of the internship program.

### 1. What is the role of SIEM in modern cybersecurity?
SIEM acts as the central nervous system for cybersecurity operations. It collects, normalizes, and analyzes logs from multiple sources to detect, investigate, and respond to threats in real-time. It helps reduce the noise and focus on the signals that matter most.

---

### 2. What challenges did you face while setting up your lab?
One of the biggest challenges was getting logs to properly flow into the Elastic Stack, especially when configuring Winlogbeat and Sysmon. Index visibility in Kibana wasn’t always immediate, and troubleshooting those forwarding issues took time and patience. Network configurations between VMs also needed careful setup to simulate real-world attacks.

---

### 3. What are the differences between Sysmon logs and Windows Security logs?
Windows Security logs focus on system-level and user authentication events (like login attempts), while Sysmon provides deeper visibility into system activity such as process creation, network connections, and file modifications. Sysmon is more behavior-based and better for detecting stealthy or advanced threats.

---

### 4. How does a brute force attack appear in logs? Mention specific Event IDs.
A brute force attack shows up as repeated `4625` (failed login) events from the same IP or username. If successful, it ends with a `4624` (successful login) event. These logs can reveal patterns like rapid-fire login attempts, which are clear signs of brute force behavior.

---

### 5. How would you detect a login outside normal business hours?
By defining business hours (e.g., 9 AM to 7 PM) and monitoring Event ID `4624` for successful logins, I filtered logins occurring late at night or early morning. Admin logins during these off-hours are red flags and should be investigated further.

---

### 6. Describe how RDP lateral movement is tracked in event logs.
Lateral movement over RDP shows up as Event ID `4624` with `LogonType` 10 (remote interactive login). If the same user logs into multiple machines across the network using RDP, it's a strong indicator of lateral movement. Monitoring these patterns helps detect the spread of an attack.

---

### 7. What is the risk of log tampering, and how can we detect it?
If an attacker deletes logs or disables logging, it can hide their actions and make detection harder. Tools like `wevtutil cl` or `auditpol` can clear logs—this activity should trigger alerts. We can detect tampering through Sysmon or by monitoring for sudden gaps or missing logs.

---

### 8. What improvements would you make in your lab setup if given more time?
I'd automate more of the log forwarding setup, integrate Security Onion or Zeek for full network packet capture, and simulate more advanced attacks like privilege escalation chains or exfiltration via DNS. I’d also work on visualizing detection logic more clearly in dashboards.

---

### 9. How will this phase help you in real-world interviews or jobs?
This phase helped me speak confidently about how threats appear in logs and how detection logic works. Knowing how to use tools like Sysmon, Winlogbeat, and Kibana gives me a strong foundation to talk about real-world use cases and incident response in interviews.

---

### 10. What was your biggest takeaway from Phase 1?
The biggest takeaway was understanding that threat detection isn’t about identifying huge malware—it’s about recognizing subtle changes and behaviors in logs. The value of a properly configured SIEM and good log hygiene cannot be overstated.
