# SSH Log Analysis Lab using Splunk

## Project Overview
For my home lab, I decided to dive into SSH log analysis using Splunk. The goal was to detect and investigate suspicious activities, such as:

 - Failed login attempts – potential brute-force or password spraying attacks.
 - Multiple failed authentications – repeated attempts signaling brute-force behavior.
 - Successful logins – to monitor legitimate access and spot possible compromised accounts.
 - Connections without authentication – signs of scanning or probing.
   
By completing this lab, I learned practical SOC Analyst skills, from log ingestion to alerts, dashboards, and visualizations.

---
## Lab Setup & Preparation
Before starting, I made sure:

1.Splunk Enterprise was installed on my system.
2.The SSH log file (ssh_log.json) was downloaded.
3.I had a basic understanding of Splunk’s Search & Reporting app.

I logged into Splunk, navigated to Apps > Search & Reporting, and uploaded the log file. I selected _json as the sourcetype to automatically extract fields and created a new index called ssh_logs.
```bash
  Tip: I realized early that proper indexing and sourcetype selection is critical for smooth searches later on
```
<!--screenshot-->
**LogIn :** <br>
![LogIn](https://github.com/user-attachments/assets/71d1f8ac-2c2d-49bf-85be-cb4685054905) <br><br>
**Search & Reporting :** <br><br>
![Search & reporting](https://github.com/user-attachments/assets/52c83256-cea1-4530-a153-6d160d9d6bdf) <br><br>
**SSH Log File Upload :** <br><br> 
![SSH Log File Uploaded](https://github.com/user-attachments/assets/8bb683dd-e876-425d-9b4b-adac8e135d6a) <br><br>
**File Uploaded :** <br><br>
![File Uploaded](https://github.com/user-attachments/assets/f11c3ace-f239-47b0-ae32-be40e2ddb6f3) <br><br>
**Selected Sourcetype _json:** <br><br>
![Selected sourcetype _json](https://github.com/user-attachments/assets/5422bb8f-133b-46dc-ba81-e0e2e1145963) <br><br>
![](https://github.com/user-attachments/assets/6078f3d6-121b-4336-afd5-cecd6ed9c03d) 

---
## Step 1: Ingesting and Parsing SSH Logs
After uploading the logs, I verified that the key fields were extracted correctly:
- event_type (Successful SSH Login, Failed SSH Login, Multiple Failed Authentication Attempts, Connection Without Authentication)
- auth_success (true/false/null)
- auth_attempts
- id.orig_h (source IP)
- id.resp_h (destination host)

I ran a validation query to check the distribution of events:
```bash
index=ssh_logs | stats count by event_type
```
<!--screenshot-->
<img width="945" height="495" alt="Image" src="https://github.com/user-attachments/assets/8555fd98-b5b6-4101-8d08-e3d6519e19d1" />

---
## Step 2: Analyzing Failed Login Attempts
I focused on identifying IP addresses with failed login attempts to detect potential brute-force activity. I ran the following search:
```bash
index=ssh_logs event_type="Failed SSH Login" | stats count by id.orig_h
```
This helped me see which source IPs were generating the most failed logins. I also created a bar chart to visualize the top 10 offending IPs. <br><br>
<img width="911" height="686" alt="Image" src="https://github.com/user-attachments/assets/ba503195-bb84-4121-b366-40c1de874d56" />

---
## Step 3: Detecting Multiple Failed Authentication Attempts

Next, I searched for IPs that attempted multiple failed logins, which could indicate brute-force attacks:
```bash
index=ssh_logs event_type="Multiple Failed Authentication Attempts" | stats count by id.orig_h, id.resp_h
```
I identified repeated failures (more than 5 attempts) and configured a Splunk alert to trigger if an IP exceeded 5 login attempts within 10 minutes. This allowed me to simulate real-time monitoring for high-risk activity.

<!--Screenshot suggestion:
Query results showing multiple failed attempts.
Screenshot of alert configuration in Splunk.-->
---
## Step 4: Tracking Successful Logins

To monitor legitimate activity and potential compromised accounts, I analyzed successful SSH logins:
```bash
index=ssh_logs event_type="Successful SSH Login" | stats count by id.orig_h, id.resp_h
```
I compared successful logins with prior failed attempts to spot any suspicious patterns. Finally, I created a dashboard panel to display the top source IPs for successful logins.

<!--Screenshot suggestion:
Query results of successful logins.
Dashboard panel showing top source IPs.-->
---
## Step 5: Identifying Connections Without Authentication
I also wanted to track potential SSH scanning attempts or incomplete connections. Using this search:
```bash
index=ssh_logs event_type="Connection Without Authentication" | stats count by id.orig_h
```
I visualized these events over time with a timechart:
```bash
index=ssh_logs event_type="Connection Without Authentication" | timechart count by id.orig_h
```
This helped me identify repeated unauthenticated attempts and possible port scanning activity.

<!--Screenshot suggestion:
Query results of unauthenticated connections.
Timechart visualization showing events over time.-->
---
## Conclusion and Learnings
By completing this lab:
- I gained hands-on experience in ingesting, parsing, and analyzing SSH logs.
- I learned to detect failed login attempts, brute-force attempts, and suspicious connections.
- I created visual dashboards and configured alerts to monitor risky activity.
- Overall, this project strengthened my SOC Analyst skills and helped me understand how to detect and respond to SSH-related security threats.

<!--Screenshot suggestion: Optional: Screenshot of the final dashboard combining all panels-->




