# 🛡️ End-to-End SOC Lab using Splunk Enterprise

![SOC](https://img.shields.io/badge/SOC-Lab-red?style=for-the-badge&logo=shield)
![Splunk](https://img.shields.io/badge/Splunk-Enterprise-black?style=for-the-badge&logo=splunk)
![Linux](https://img.shields.io/badge/Linux-Mint-87CF3E?style=for-the-badge&logo=linux&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-VM-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

> **Collect. Analyze. Detect. Alert.**  
> A hands-on SOC lab simulating real-world log collection, threat detection, and alerting using Splunk Enterprise.

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Lab Setup](#-lab-setup)
- [Configuration](#-configuration)
- [Attack Simulation & Detection](#-attack-simulation--detection)
- [Alert Creation](#-alert-creation)
- [Troubleshooting](#-troubleshooting)
- [SSL Exploration](#-ssl-secure-log-transmission)
- [Skills Demonstrated](#-skills-demonstrated)
- [Project Report](#-project-report)
- [Future Improvements](#-future-improvements)

---

## 🔍 Overview

This project demonstrates the design and implementation of an **end-to-end Security Operations Center (SOC) lab** using **Splunk Enterprise** and **Splunk Universal Forwarder**.

The lab simulates a real-world SOC environment where:
- Logs are **collected** from a Linux Mint host machine
- Forwarded **securely** to a centralized Splunk server (Ubuntu VM)
- **Analyzed** in real-time for suspicious activity
- **Alerts** are triggered on detecting potential threats (SSH brute-force)

---

## 🏗️ Architecture

![SOC Diagram](digram_soc_a_%20Image%20May%2018,%202026,%2012_55_10%20PM.png)

| Component | Role |
|---|---|
| **Linux Mint (Host)** | Log Generator + Universal Forwarder |
| **Splunk Universal Forwarder** | Collects & forwards logs to Splunk |
| **VirtualBox** | Virtualization layer |
| **Ubuntu VM** | Runs Splunk Enterprise (SIEM Server) |
| **Splunk Web (Port 8000)** | Dashboard, Search, Alerts |

**Ports Used:**
- `9997` → Log Forwarding (TCP)
- `8000` → Splunk Web Interface

---

## 🖥️ Lab Setup

### Host Machine — Linux Mint
- Installed **Splunk Universal Forwarder**
- Monitors system logs:
  - `/var/log/auth.log`
  - `/var/log/syslog`
  - `/var/log/kern.log`
  - `/var/log/daemon.log`
  - `/var/log/secure`

### Virtual Machine — Ubuntu (VirtualBox)
- Installed **Splunk Enterprise**
- Configured to receive logs on **port 9997**
- Accessible via browser on **port 8000**

---

## ⚙️ Configuration

### 1. Enable Data Receiving on Splunk
```
Settings → Forwarding and Receiving → Configure Receiving → Add Port 9997
```

### 2. outputs.conf — Forward logs to Splunk Server
```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = <Splunk_Server_IP>:9997
```

### 3. inputs.conf — Monitor Authentication Logs
```ini
[monitor:///var/log/auth.log]
disabled = false
```

### 4. Restart the Forwarder
```bash
sudo /opt/splunkforwarder/bin/splunk restart
```

### 5. Verify Network Connectivity
```bash
ping <Splunk_Server_IP>
```

---

## ⚔️ Attack Simulation & Detection

### Simulating SSH Brute-Force Attack
```bash
ssh wronguser@localhost
# Repeat multiple times to generate failed login events
```

### Detecting Failed Login Attempts in Splunk
```spl
index=* "Failed password"
```

This query surfaces repeated failed SSH login attempts — a key indicator of a **brute-force attack**.

### Verify All Logs Are Ingesting
```spl
index=*
```

---

## 🔔 Alert Creation

An automated alert was configured in Splunk to detect suspicious login activity:

| Setting | Value |
|---|---|
| **Alert Name** | Brute Force Detection |
| **Search Query** | `index=* "Failed password"` |
| **Trigger Condition** | More than 5 results in 5 minutes |
| **Alert Type** | Scheduled |
| **Action** | Add to Triggered Alerts |

**Steps:**
1. Run the detection search query
2. Click **Save As → Alert**
3. Set trigger condition and schedule
4. Save and enable the alert

---

## 🔧 Troubleshooting

| Problem | Solution |
|---|---|
| Forwarder not connecting | Verify IP in `outputs.conf`, check port 9997, use Bridged Adapter in VirtualBox |
| Logs not appearing | Check `inputs.conf` path, verify log file exists, restart forwarder |
| Alert not triggering | Reduce threshold for testing, generate more failed attempts, check time range |

**Useful commands:**
```bash
# Check forwarder status
sudo /opt/splunkforwarder/bin/splunk status

# Check forwarder connection
sudo /opt/splunkforwarder/bin/splunk list forward-server

# Check internal Splunk logs
index=_internal
```

---

## 🔐 SSL Secure Log Transmission

Explored SSL encryption between Universal Forwarder and Splunk server:
- Generated self-signed certificate using **OpenSSL**
- Configured `inputs.conf` on server for SSL receiving
- Configured `outputs.conf` on forwarder with SSL parameters

**Outcome:** Encountered certificate validation errors; configuration rolled back to maintain lab stability. Gained hands-on experience in SSL certificate management and troubleshooting.

---

## 💡 Skills Demonstrated

| Category | Skills |
|---|---|
| **SIEM** | Splunk Enterprise, Log Indexing, SPL Queries |
| **Threat Detection** | Brute-force Detection, Failed Login Analysis |
| **Log Management** | Log Ingestion, Forwarding, Retention |
| **Linux Administration** | Ubuntu, Linux Mint, CLI Operations |
| **Networking** | TCP Forwarding, Port Configuration, Connectivity Testing |
| **Security** | Alert Configuration, SSL Exploration, SOC Operations |
| **Troubleshooting** | Config Debugging, Service Management, Log Analysis |

---

## 📄 Project Report

[📥 Download Full SOC Lab Report (PDF)](./Preet_Splunk_SOC_Lab_Project-compressed.pdf)

---

## 🚀 Future Improvements

- [ ] Create Splunk dashboards for visual threat monitoring
- [ ] Simulate advanced attacks using **Kali Linux**
- [ ] Integrate multiple log sources (Apache, Syslog, Windows Event Logs)
- [ ] Implement **proper SSL** between forwarder and server
- [ ] Add automated response mechanisms (**SOAR basics**)
- [ ] Develop correlation rules and SIEM use cases
- [ ] Centralize logs from multiple systems

---

## 👤 Author

**Preet** | Cybersecurity Enthusiast | Aspiring SOC Analyst  
GitHub: [@preet-cyber29](https://github.com/preet-cyber29)

---

*"Logs Today. Insights Tomorrow. Security Always."*
