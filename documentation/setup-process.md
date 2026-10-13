# SOC Monitoring Lab Setup Using Graylog

## Overview

This project demonstrates the design and implementation of a small Security Operations Center (SOC) monitoring environment using Graylog. The lab collects authentication logs from a Linux system, analyzes them for suspicious activity, and generates alerts when predefined security conditions are met.

The purpose of the lab is to simulate real-world log monitoring and detection engineering practices used by SOC analysts.

---

## Lab Environment

The SOC lab was built using the following components:

| Component | Version |
|----------|--------|
| Ubuntu | 22.04 LTS |
| Graylog | 4.3.15 |
| OpenSearch | 1.3.x |
| MongoDB | 7.0.x |
| Log Forwarder | rsyslog |
| Alerting | Gmail SMTP |

---

## Architecture

The log flow in this lab is as follows:

Linux System Logs  
→ rsyslog  
→ Graylog Syslog TCP Input  
→ Graylog Stream Rules  
→ Event Definitions (Detection Rules)  
→ Email Notifications  
→ Dashboard Visualization

Graylog receives logs, processes them, and stores them in OpenSearch for indexing and searching.

MongoDB is used to store Graylog configuration data such as users, streams, and alerts.

---

## Installing MongoDB

MongoDB is required by Graylog to store metadata and configuration information.

Installation steps:

1. Add MongoDB repository.
2. Install MongoDB.
3. Start the MongoDB service.

Verify installation: sudo systemctl status mongod


MongoDB should show as **active (running)**.

---

## Installing OpenSearch

OpenSearch is the indexing engine used by Graylog to store and search log data.

Verify OpenSearch installation:
curl http://localhost:9200

The output should display OpenSearch cluster information in JSON format.

---

## Installing Graylog

Graylog was installed using the official Graylog repository.

Steps:

1. Add the Graylog repository.
2. Install the Graylog server package.
3. Configure the Graylog server.

Configuration file:
/etc/graylog/server/server.conf


Important configuration parameters include:
password_secret
root_password_sha2
elasticsearch_hosts = http://127.0.0.1:9200

After editing the configuration file, restart Graylog:
sudo systemctl restart graylog-server
Verify Graylog is running:


sudo systemctl status graylog-server


---

## Accessing the Graylog Web Interface

Once Graylog is running, the web interface can be accessed at:


http://localhost:9000


Login credentials:


Username: admin
Password: configured during setup


---

## Creating a Syslog TCP Input

To receive logs from the system, a Syslog TCP input was created.

Steps:

1. Navigate to **System → Inputs**
2. Select **Syslog TCP**
3. Launch a new input
4. Configure port **5140**
5. Set the input to **Global**

This allows Graylog to receive logs forwarded from the system.

---

## Configuring rsyslog Log Forwarding

The system logs were forwarded to Graylog using rsyslog.

Configuration file:


/etc/rsyslog.conf


The following line was added:


. @@127.0.0.1:5140


Explanation:

- `*.*` forwards all logs
- `@@` sends logs using TCP
- `127.0.0.1:5140` sends logs to Graylog

After editing the configuration file, restart rsyslog:


sudo systemctl restart rsyslog


---

## Verifying Log Ingestion

To verify that logs are arriving in Graylog:

1. Navigate to the **Search** page.
2. View incoming logs in real time.

Authentication logs such as SSH login attempts should now appear in Graylog.

---

## Email Alert Configuration

Email alerts were configured using Gmail SMTP.

Configuration parameters added to:


/etc/graylog/server/server.conf


Example configuration:


transport_email_enabled = true
transport_email_hostname = smtp.gmail.com
transport_email_port = 587
transport_email_use_auth = true
transport_email_use_tls = true
transport_email_auth_username = your-email@gmail.com

transport_email_auth_password = Gmail-App-Password
transport_email_from_email = your-email@gmail.com


A Gmail **App Password** was used instead of the normal account password for security reasons.

After configuration, Graylog was restarted.

---

## Dashboard Creation

A SOC dashboard was created to visualize authentication activity.

Widgets include:

- Failed SSH login attempts
- Successful login events
- Sudo activity
- Log source counts

This dashboard provides a centralized view of security activity on the system.

---

## Conclusion

The Graylog SOC lab successfully collects and analyzes authentication logs, detects suspicious login behavior, and generates alerts for security monitoring.

This environment simulates a basic SOC monitoring workflow used by security analysts.



