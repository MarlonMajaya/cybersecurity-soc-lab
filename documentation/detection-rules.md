# Detection Rules

Detection rules were created in Graylog using Event Definitions to identify suspicious activity in authentication logs.

---

## SSH Brute Force Detection

Objective:

Detect repeated failed SSH login attempts from the same source.

Query used: message:"Failed password"


Configuration:

- Search within: 5 minutes
- Execute search every: 1 minute
- Group by: source IP
- Condition: count() > 10

Alert Trigger:

If more than 10 failed login attempts occur within 5 minutes from the same source, an alert is triggered.

Purpose:

This rule helps detect brute-force attacks where an attacker repeatedly attempts to guess passwords.

---

## Successful Login Monitoring

Objective:

Detect successful SSH login events.

Query used: message:"Accepted password"


Purpose:

Successful logins following multiple failed attempts may indicate a compromised account.

Monitoring these events helps analysts investigate suspicious login activity.

---

## Sudo Activity Monitoring

Objective:

Monitor privilege escalation activity.

Query used: message:"sudo"

Purpose:

Sudo commands allow users to execute administrative actions. Monitoring sudo activity helps detect unauthorized privilege escalation attempts.

---

## Alert Notifications

When detection rules are triggered, Graylog sends an email notification using the configured SMTP server.

Alert notifications include:

- Event name
- Timestamp
- Source IP
- Event details



