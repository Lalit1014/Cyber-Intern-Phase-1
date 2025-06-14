# SIEM Internship Program – Phase 1: Cyber Defense Lab
Phase 1 of the SIEM Internship Program. This project establishes foundational skills in Security Information and Event Management (SIEM) by building a detection lab, simulating attacks, and using Elastic Stack to identify threats.

## 🎯 Objectives:

Build and configure a SIEM lab with Elastic Stack.
Simulate 10 real-world attack techniques.
Forward and analyze logs from Windows and Linux systems.
Detect threats using Sysmon, Winlogbeat, and Elastic SIEM.

## 🛠️ Lab Components

| Component         | Description                                         |
|------------------|------------------------------------------------------|
| VirtualBox        | Virtualization platform for running VMs             |
| Kali Linux        | Attacker machine (Linux)                            |
| Windows 10        | Target machine for attack simulation                |
| Sysmon            | Logs detailed Windows activities                    |
| Splunk Enterprise | SIEM used for log ingestion and analysis            |
| Splunk Forwarder  | Sends logs from Windows 10 to Splunk on Kali Linux  |

---

## 🧱 Splunk Server Setup (Host Machine)

1. Download and install **Splunk Enterprise** from [Splunk Downloads](https://www.splunk.com/en_us/download.html).
2. Start the Splunk service and log in via `http://localhost:8000`.
3. Create a new index (e.g., `lab_index`) for log segregation.

---

## 🪟 Windows VM Setup

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

## 🛠 Install and Start Sysmon on Windows

    Download Sysmon from:
    https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

    Download Sysmon config (recommended):
    https://github.com/SwiftOnSecurity/sysmon-config

    Install command (in Command Prompt as Administrator):

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
├── logs/ # Contains exported logs (JSON, TXT, etc.)
├── screenshots/ # Screenshots of setup, configurations, dashboards
├── reports/ # Summary notes, setup steps, attack findings
├── hints/ # Helpful links, commands, tips, cheat sheets
├── configs/ # Sysmon, Winlogbeat, Splunk Forwarder config files
└── README.md # Project overview and setup guide
```
---

## ✅ Setup Checklist

| Item                                      | Status   |
|-------------------------------------------|----------|
| VirtualBox/VMware Installed               | ✅       |
| Kali Linux VM ready                       | ✅       |
| Windows 10 VM ready                       | ✅       |
| Sysmon                                    | ✅       |
| Splunk Enterprise & Forwarder setup complete, Sysmon installed | ✅       |
| GitHub repo created                       | ✅       |

---
