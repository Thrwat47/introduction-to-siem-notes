# TryHackMe — Introduction to SIEM

**Completed:** 10/3/2026

## Overview
In this room I learned what a SIEM (Security Information and Event Management) system is, why a SOC analyst needs it, how logs from different sources are collected and normalized, and how alerts are created and investigated.

## Learning Objectives
- Understand the different types of log sources
- Identify the limitations of working with isolated logs
- Recognize the importance of a SIEM solution
- Explore the features of a SIEM solution
- Learn various types of log sources and their ingestion in the SIEM
- Understand the process behind alerting and alert analysis

## Key Concepts

### Logs Everywhere, Answers Nowhere
Devices in a network continuously generate logs. These devices are called **log sources**, and they fall into two categories:

**1) Host-centric log sources** capture events on the host itself (Windows, Linux, servers):
- A user accessing a file or trying to authenticate
- Process execution
- Registry key changes
- PowerShell execution

**2) Network-centric log sources** are generated when hosts communicate (firewalls, IDS/IPS, routers):
- SSH connections
- FTP file access
- Web traffic
- VPN access
- Network file sharing activity

**Challenges of working with isolated logs:**
- **Numerous log sources:** hundreds of events per second across many devices.
- **No centralization:** logs stay on each machine, so the analyst has to connect to each source one by one.
- **Limited context:** one log alone may look harmless, but correlated logs can show a different story (for example, lateral movement).
- **Limited analysis:** it is nearly impossible for humans to analyze all logs manually.
- **Format issues:** different sources produce different formats.

### Features of SIEM
- **Centralized log collection:** logs from endpoints, servers, and firewalls are pulled into one place using agents or APIs.
- **Normalization:** logs are broken into fields (parsing) and converted into one consistent format.
- **Correlation:** linking logs from different sources to find patterns. Example: a VPN login from a new IP, then file access, then a PowerShell script, then an outbound connection can point to data exfiltration with stolen credentials.
- **Real-time alerting:** alerts trigger when logs match detection rules.
- **Dashboards and reporting:** summaries such as alert highlights, failed login attempts, events ingested count, rules triggered, and top domains visited.

### Log Sources and Ingestion

**Linux log locations:**
- `/var/log/httpd` — HTTP request/response and error logs
- `/var/log/cron` — cron job events
- `/var/log/auth.log` and `/var/log/secure` — authentication logs
- `/var/log/kern` — kernel events

**Web server logs:** Apache logs are in `/var/log/apache` or `/var/log/httpd`.

**Log ingestion methods:**
1. **Agent / Forwarder:** a lightweight tool installed on the endpoint that sends important logs to the SIEM server.
2. **Syslog:** a widely used protocol to send real-time data from systems to a central destination.
3. **Manual upload:** upload offline data for quick analysis (Splunk and ELK support this).
4. **Port-forwarding:** the SIEM listens on a port and endpoints forward data to it.

### Alerting Process and Analysis
Detection rules are logical expressions that trigger an alert when their conditions are met. Examples:
- 5 failed logins in 10 seconds: alert for multiple failed login attempts
- Successful login after multiple failed attempts: alert
- Outbound traffic larger than 25 MB: possible data exfiltration

**Use case 1: Event log cleared**
- Attackers clear logs to hide their tracks. Event ID **104** is logged when event logs are cleared.
- Rule: if log source is WinEventLog AND EventID is 104, trigger "Event Log Cleared".

**Use case 2: whoami command**
- Attackers run commands like `whoami` after exploitation.
- Rule: if log source is WinEventLog AND EventCode is 4688 AND NewProcessName contains `whoami`, trigger an alert.

**Alert investigation:** the analyst reviews the events behind the alert, checks which rule conditions were met, and decides if it is a true or false positive. Possible actions:
- False positive: tune the rule to avoid similar alerts.
- True positive: investigate further, contact the asset owner, isolate the infected host, block the suspicious IP.

## Lab Work
In the static lab, I used a sample SIEM dashboard and event log to investigate an alert:
- After clicking "Start Suspicious Activity", the dashboard showed a suspicious process: `cudominer.exe`.
- I checked the event (Event ID 4688, process creation) to find the user and hostname responsible.
- I checked the rule and found that the term `miner` in the process name triggered the alert.
- I classified the alert as a **true positive** and selected the right action to finish the lab.

## What I Learned
- A SIEM gives the SOC one central place to see logs instead of checking each device by hand.
- Normalization (one format for all logs) makes searching and comparing logs much easier.
- Correlation connects separate events that look harmless alone into one attack story.
- Detection rules are simple logic on fields like Event ID and process name, which is why normalized logs matter.
- An alert is not always an attack: the analyst decides true or false positive, and false positives mean the rule needs tuning.
- This connects to my Windows Logging notes: Event ID 4688 from the Security log is exactly the kind of data a SIEM rule uses.

## Screenshot


<img width="1920" height="946" alt="Screenshot 2026-10-15 061105" src="https://github.com/user-attachments/assets/22bd8ec6-b4a2-490d-931c-db7166d8e151" />









