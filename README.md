# 🛡️ SSH Brute Force Detection Lab

> **SOC Analysis Lab** | Splunk SIEM · Hydra · Linux Auth Logs · SPL

---

## 📌 Overview

In this lab, I simulated a real-world **SSH Brute Force attack** and built a full detection pipeline using **Splunk**. The goal was to replicate what a SOC Analyst would do when responding to a credential-stuffing or brute-force incident.

---

## 🎯 Objectives

- Simulate a realistic brute-force attack against an SSH service
- Ingest and parse Linux authentication logs into Splunk
- Develop SPL queries to detect attack patterns
- Build an automated alerting system for real-time detection
- Visualize attack data through an interactive SOC dashboard

---

## 🧰 Tools Used

| Tool | Purpose |
|------|---------|
| **Hydra** | Attack simulation — generating SSH login attempts |
| **Splunk Enterprise** | SIEM platform for log ingestion and analysis |
| **Splunk Universal Forwarder** | Forwarding Linux auth logs to Splunk |
| **Linux (auth.log)** | Log source containing SSH authentication events |
| **SPL** | Search Processing Language for threat detection |

---

## 🔬 Lab Architecture

```
[Kali Linux - Attacker]
        |
        | SSH Brute Force (Hydra)
        ↓
[Ubuntu Target Machine]
        |
        | auth.log → Splunk Universal Forwarder
        ↓
[Splunk Enterprise - SIEM]
        |
        | SPL Queries → Alerts → Dashboard
```

---

## 🚀 Step-by-Step Walkthrough

### Step 1: Attack Simulation (Hydra)

Simulated **1,000+ failed SSH login attempts** from a Kali Linux machine using Hydra to generate realistic authentication events in `/var/log/auth.log`.

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://TARGET_IP -t 4
```

**What this generates:** Hundreds of `Failed password` entries in auth.log, mimicking a real brute-force attack.

![Attack Simulation](Screenshot%202026-04-29%20093432.png)

---

### Step 2: Log Ingestion (Splunk Universal Forwarder)

Configured the **Splunk Universal Forwarder** on the Ubuntu target to ship auth.log to Splunk Enterprise in real time.

```bash
# Monitor auth.log
[monitor:///var/log/auth.log]
index = linux_logs
sourcetype = syslog
```

---

### Step 3: Detection with SPL

Developed custom **SPL queries** to identify brute-force patterns:

```spl
index=linux_logs sourcetype=syslog "Failed password"
| stats count by src_ip, user
| where count > 10
| sort - count
```

**What this detects:** Any IP with more than 10 failed login attempts — a strong indicator of brute-force activity.

![Splunk Analysis](Screenshot%202026-04-29%20093632.png)

---

### Step 4: Alerting & Dashboard

- Built a **real-time Splunk alert** to trigger when failed attempts exceed a threshold
- Created an **interactive dashboard** showing:
  - Top attacking IPs
  - Failed login timeline
  - Targeted usernames
  - Attack frequency heatmap

![Dashboard](Screenshot%202026-04-29%20093911.png)

---

## 🔍 Key Findings

| Indicator | Value |
|-----------|-------|
| Total login attempts | 1,000+ |
| Unique source IPs detected | Simulated attacker IP |
| Detection threshold | >10 failed attempts |
| Time to detection | Real-time via Splunk alert |

---

## 🧠 MITRE ATT&CK Mapping

| Technique | ID | Description |
|-----------|----|-------------|
| Brute Force | T1110 | Attempting to gain access via repeated login attempts |
| Valid Accounts | T1078 | Goal of the attack — obtaining valid credentials |
| Remote Services | T1021.004 | SSH used as the attack vector |

---

## 📚 What I Learned

- How attackers use tools like Hydra to automate credential attacks
- How to configure Splunk Universal Forwarder for Linux log collection
- Writing effective SPL queries for brute-force detection
- Building SOC dashboards for real-time visibility
- Setting up automated alerts to reduce response time

---

## 👤 Author

**Abdelrahman Mohamed Hussein** — SOC Analyst  
[LinkedIn](https://www.linkedin.com/in/abd-el-rahman-mohamed-hussein-a6071b256) | [GitHub](https://github.com/abdelrahman-28m)# SSH-Brute-Force-Detection
