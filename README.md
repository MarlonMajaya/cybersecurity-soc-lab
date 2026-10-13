# Cybersecurity-SOC-Lab

SOC monitoring lab built using Graylog, OpenSearch, and Ubuntu. Includes log ingestion, detection engineering, alerting, and dashboards.

## Project Overview

This project demonstrates a small Security Operations Center (SOC) monitoring environment built using Graylog. The lab collects system authentication logs, detects suspicious activities, generates alerts, and visualizes security events on a dashboard.

The objective of this project is to simulate real-world security monitoring and detection engineering tasks performed by SOC analysts.

> **Scope note:** this is a single-host lab built for learning. All activity was generated against systems I own, on an isolated lab network.

## Lab Architecture

The SOC lab consists of a single Ubuntu system generating authentication logs which are forwarded to a Graylog server using rsyslog over Syslog TCP.

Graylog processes the logs, applies detection rules, and stores indexed log data in OpenSearch for searching and analysis. MongoDB is used by Graylog to store configuration and metadata.

When suspicious activity is detected, Graylog triggers alerts and sends email notifications to the analyst.

**Log Flow:**

```
Ubuntu System Logs
        ↓
rsyslog (Syslog TCP)
        ↓
Graylog SIEM
        ↓
Detection Rules / Streams
        ↓
OpenSearch (Log Storage)
        ↓
Alert Notifications (Email)
```

## Technologies Used

- Graylog 4.3.15
- OpenSearch 1.3.x
- MongoDB 7.0
- Ubuntu 22.04
- rsyslog
- Gmail SMTP (Email Alerts)

## Key Features

- Centralized log collection
- SSH authentication monitoring
- Brute force attack detection
- Privilege escalation monitoring
- Email alert notifications
- Security dashboard visualization

## Detection Rules Implemented

- SSH Brute Force Detection
- Successful SSH Login Monitoring
- Sudo Privilege Escalation Monitoring

### MITRE ATT&CK Mapping

| Detection | Technique |
|---|---|
| SSH Brute Force Detection | T1110 – Brute Force |
| Successful login after brute-force attempts | T1078 – Valid Accounts |
| Sudo Privilege Escalation Monitoring | T1548.003 – Abuse Elevation Control Mechanism: Sudo and Sudo Caching |

### Detection Logic (examples)

The detections are built on the standard Ubuntu authentication log messages. Example search patterns (field names and thresholds depend on your Graylog setup):

| Detection | Log pattern |
|---|---|
| Failed SSH login | `message:"Failed password" AND message:"sshd"` |
| Successful SSH login | `message:"Accepted password" OR message:"Accepted publickey"` |
| Sudo command run | `message:"sudo" AND message:"COMMAND="` |
| Unauthorized sudo attempt | `message:"NOT in sudoers"` |

Brute force is detected by counting failed logins from the same source IP within a short time window, and alerting when the count crosses a threshold.

## Example Alerts

The system generates alerts when suspicious activity is detected such as:

- Multiple failed login attempts
- Multiple failed login attempts then a successful attempt from same IP address
- Successful login after brute-force attempts
- Unauthorized sudo activity

## Incident Response and Remediation

When a detection rule is triggered, Graylog generates an alert and sends an email notification to the analyst.

In a real SOC environment, these alerts would typically create incident tickets in an incident management platform such as ServiceNow or Jira for investigation.

**Typical analyst response workflow:**

1. Alert is received via email notification.
2. Analyst reviews the logs in Graylog.
3. The source IP and user activity are investigated.
4. If malicious activity is confirmed, remediation actions may include:
   - Blocking the attacker IP address
   - Disabling compromised user accounts
   - Investigating additional related logs

This project simulates the detection and alerting phase of the SOC workflow while demonstrating how incidents would be investigated and mitigated.

## SOC Dashboard

The dashboard provides visualization of security activity including:

- Failed SSH login attempts
- Successful logins
- Sudo command activity
- Log source distribution

## Quick Start

1. Install MongoDB, OpenSearch and Graylog on the SIEM server (versions listed above).
2. In Graylog, create a **Syslog TCP** input (for example on port 5140).
3. On the Ubuntu host, add a forwarding rule for authentication logs to `/etc/rsyslog.d/`:
   ```
   auth,authpriv.* @@<graylog-server-ip>:5140
   ```
   then restart rsyslog: `sudo systemctl restart rsyslog`
4. Import the content pack from this repository (streams, alerts, dashboards and inputs).
5. Configure the email notification with your own SMTP details.
6. Trigger test events and confirm the alerts and dashboard update. See `documentation/testing-scenarios.md`.

Full steps are in `documentation/setup-process.md`.

## Security Considerations

- No passwords, SMTP app passwords, API tokens or private keys are stored in this repository. Use your own values when you import the content pack.
- Gmail SMTP requires an app password. Keep it out of version control.
- The Syslog input should only be reachable from the lab network, not the internet.
- All testing was performed on systems I own, in an isolated lab environment.

## Repository Structure

```
Cybersecurity-SOC-Lab
│
├── README.md
├── content-pack
├── documentation
│   ├── setup-process.md
│   ├── detection-rules.md
│   └── testing-scenarios.md
│
└── screenshots
```

## Skills Demonstrated

- Security monitoring
- Log analysis
- SIEM configuration
- Detection rule creation
- Alerting and incident detection
- SOC dashboard creation
- Linux authentication log analysis
- MITRE ATT&CK mapping

## Graylog Content Pack

This repository includes a Graylog content pack that allows the SOC lab configuration (streams, alerts, dashboards, and inputs) to be imported into another Graylog instance.

## Limitations

- Single log source (one Ubuntu host), so detections are tuned for lab traffic and would need tuning before production use.
- Alerts are delivered by email only and do not create tickets automatically.
- Response actions (blocking an IP, disabling an account) are done manually.

## Future Improvements

- Add Windows event log monitoring
- Integrate threat intelligence feeds
- Implement log pipelines
- Add correlation rules
- Add GeoIP enrichment for source IP addresses
- Automate response, for example blocking repeat offenders with fail2ban
- Send alerts to a ticketing system (Jira or ServiceNow) via webhook

## Author

Marlon Majaya

[GitHub](https://github.com/MarlonMajaya) · [LinkedIn](https://www.linkedin.com/in/marlon-majaya-130934382/) · [Medium](https://medium.com/@marlonmajaya77)
