# splunk-security-logs
I've created a GitHub-friendly README for your Splunk Security Log Analysis project. It includes setup instructions, log forwarding configurations, Splunk queries, dashboards, security alerts, and compliance reporting.
# Splunk Security Log Analysis

## 📌 Project Overview
This project configures Splunk to collect, analyze, and monitor security logs from Windows and Linux machines. The setup improves visibility into security events, enhances incident detection, and supports compliance with security frameworks such as ISO 27001 and NIST.

## 🚀 Objectives
- Configure Splunk for centralized log collection.
- Forward security logs from Windows and Linux machines.
- Build dashboards for real-time security monitoring.
- Detect security incidents and trigger alerts.
- Generate compliance reports.

---

## 🛠️ Setup & Configuration

### 1️⃣ Install Splunk Enterprise
1. Download Splunk Enterprise from [Splunk Downloads](https://www.splunk.com/en_us/download.html).
2. Install Splunk on a **Linux/Windows** server.
   ```sh
   wget -O splunk.tgz 'https://download.splunk.com/products/splunk/releases/latest/linux/splunk.tgz'
   tar -xvzf splunk.tgz -C /opt
   cd /opt/splunk/bin
   ./splunk start --accept-license
   ```
3. Set Splunk to start on boot:
   ```sh
   ./splunk enable boot-start
   ```

### 2️⃣ Configure Log Forwarding

#### 🔹 Windows (Splunk Universal Forwarder)
1. Download and install the **Splunk Universal Forwarder**.
2. Configure **WinEventLog** monitoring:
   ```sh
   splunk add monitor 'WinEventLog://Security'
   splunk add monitor 'WinEventLog://System'
   ```
3. Configure `inputs.conf` (located in `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`):
   ```ini
   [WinEventLog://Security]
   disabled = 0
   index = security
   ```
4. Restart the Splunk Forwarder:
   ```sh
   splunk restart
   ```

#### 🔹 Linux (Syslog & Auditd)
1. Configure **rsyslog** for log forwarding:
   ```sh
   sudo nano /etc/rsyslog.conf
   ```
   Add:
   ```
   *.* @@splunk-server:514
   ```
2. Restart the service:
   ```sh
   sudo systemctl restart rsyslog
   ```
3. Configure **auditd** for system auditing:
   ```sh
   sudo auditctl -a always,exit -F arch=b64 -S execve -k command_exec
   ```

### 3️⃣ Splunk Data Inputs Configuration
- Add data inputs from **Splunk Web UI** → **Settings** → **Data Inputs** → **Files & Directories**.
- Set index = `security` for consistency.

---

## 📊 Data Analysis & Dashboards
### 🔹 Security Event Queries (SPL)
✅ **Failed Login Attempts (Windows)**
```spl
index=security sourcetype="WinEventLog:Security" EventCode=4625
| stats count by Account_Name, Source_Network_Address
```
✅ **User Account Changes (Linux)**
```spl
index=security sourcetype=linux_audit action=add_user OR action=delete_user
| stats count by user, host
```
✅ **SSH Access Tracking**
```spl
index=security sourcetype=linux_secure "Accepted password"
| stats count by user, src_ip
```

### 🔹 Splunk Dashboard Examples
- **Real-time Login Activity**
- **Malware & Firewall Log Analysis**
- **Admin Privilege Escalation Events**

---

## 🚨 Security Alerting
1. Set up **Real-time Alerts** for:
   - Multiple failed login attempts (**Brute Force Detection**)
   - Admin/root login from new IPs
   - File integrity changes in `/etc/passwd`

2. Example Alert Query:
   ```spl
   index=security sourcetype=linux_secure "Failed password" 
   | stats count by src_ip, user 
   | where count > 5
   ```

---

## 📜 Compliance & Reporting
- **ISO 27001**: Log retention & incident monitoring.
- **NIST Framework**: Security analytics for threat detection.
- **Automated Reports** for auditing security incidents.

---

## 🔧 Future Enhancements
✅ Integrate **Splunk with Threat Intelligence Feeds**.
✅ Implement **Machine Learning-based Anomaly Detection**.
✅ Automate **Incident Response using SOAR**.

---

## 📌 Technologies Used
- **Splunk Enterprise**
- **Splunk Universal Forwarder**
- **Windows Event Logs**
- **Linux Syslog & Auditd**
- **Security Information and Event Management (SIEM)**

---

## 📢 Contribution & Feedback
Feel free to **fork** this repository, contribute by submitting **pull requests**, or suggest improvements via **issues**! 🚀

---

### 🔗 References
- [Splunk Documentation](https://docs.splunk.com/Documentation/Splunk)
- [Windows Event ID Cheat Sheet](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia.aspx)
- [Linux Syslog Configuration Guide](https://www.rsyslog.com/doc/master/index.html)
