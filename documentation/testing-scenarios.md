# Testing Scenarios

To verify that the detection rules function correctly, several attack simulation scenarios were performed.

---

## SSH Brute Force Simulation

Scenario:

Multiple failed SSH login attempts were generated using incorrect credentials.

Command used:


ssh fakeuser@localhost


Incorrect passwords were entered repeatedly.

Expected Result:

The brute-force detection rule triggers an alert when the number of failed attempts exceeds the threshold.

Outcome:

Graylog successfully generated an alert and displayed the events on the dashboard.

---

## Successful Login Test

Scenario:

After multiple failed login attempts, a successful login was performed.

Expected Result:

The successful login event appears in the logs and is visible in the dashboard widgets.

Purpose:

This helps demonstrate how a successful login following failed attempts may indicate account compromise.

---

## Sudo Activity Test

Scenario:

Administrative commands were executed using sudo.

Example command:


sudo ls


Expected Result:

Sudo events appear in Graylog logs and are visible in the dashboard.

Purpose:

This verifies that privilege escalation activity is correctly monitored.

---

## Email Alert Test

Scenario:

A brute-force attack simulation was performed.

Expected Result:

Graylog sends an email alert when the detection rule is triggered.

Outcome:

Email notification was successfully received, confirming that alerting works correctly.
