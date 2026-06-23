**Platfrom:** LetsDefend

**Lab Name:** SOC168--Whoami-Command-Detected-in-Request-Body

**Difficulty:** Easy

**Date Completed:** Jun 23, 2026

**Type:** Web Attack

**Target:** WebServer004 (172.16.17.16)

---
<h3 align =center> Goal </h3>

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Investigate wwhether a command injection attack occurred, determine if it was successful, contain the affected host and document findings
<br>
<br>

---
<h3 align =center> Walkthrough </h3>

***Alert Review:***

<br>
&nbsp;&nbsp;&nbsp;&nbsp; Opening the alert in the Monitoring tab under the Investigation Channel reveals all the critical details. 
<br>
<br>

<img width="1020" height="347" alt="1" src="https://github.com/user-attachments/assets/cdcf7ffe-03de-4971-aa31-44eeea625913" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; This alert tells us that the string "whoami" was found inside the body of an HTTP request sent to one of our web servers.

&nbsp;&nbsp;&nbsp;&nbsp; Seeing it in an HTTP request body is a strong signal that someone is attempting command injection — a technique where an attacker embeds operating system commands into web requests. Now, let's open the playbook.
<br>

---
***Starting the Playbook:***

<img width="592" height="360" alt="2" src="https://github.com/user-attachments/assets/9c5f6571-a4e4-474f-9961-b1de9e7d7c9e" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The rule triggered because command injection code was detected inside the requested URL. Specifically, look at the URL:
<br>
<br>

```bash
https://172.16.17.16/video/
```

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; This URL contain malicious command "whoami". Attacker run it immediately after gaining access to a machine to find out what user account they are operating under.
<br>
<br>

<img width="591" height="367" alt="3" src="https://github.com/user-attachments/assets/41e39972-0004-417a-870d-e4776748c3e8" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Our next move is to look up 61.177.172.87 in VirusTotal to understand its reputation.
<br>
<br>

<img width="1256" height="698" alt="4" src="https://github.com/user-attachments/assets/6bfc5b7a-1db6-4dcb-b818-f5fd7e586283" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; VirusTotal shows 3/91 vendors flagged this IP as malicious. The IP belongs to AS4134(Chinanet), registered under the APNIC regional registry.
<br>
<br>

---
***Log Investigation:***

<img width="588" height="417" alt="5" src="https://github.com/user-attachments/assets/4f8cdb73-30be-4710-b9e4-7a5e055cf145" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Now we have to see the raw logs to understand exactly what commands were sent and when.
<br>
<br>

<img width="852" height="286" alt="6" src="https://github.com/user-attachments/assets/ee05f4b8-0c14-48a0-bc59-83110f8b009c" />

<img width="852" height="286" alt="7" src="https://github.com/user-attachments/assets/11694ca2-52f3-4217-a6db-a334e197bc2f" />

<img width="839" height="289" alt="8" src="https://github.com/user-attachments/assets/df6c8dff-599f-4b76-9d00-335949b7f998" />

<img width="860" height="282" alt="9" src="https://github.com/user-attachments/assets/67e90c0b-b6ce-4247-8f52-09eaad30b66e" />

<img width="852" height="302" alt="10" src="https://github.com/user-attachments/assets/cc1647b2-4df1-4b66-9d0d-2d0a74f3734a" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; What you are seesing here is a classic command injection attack sequence. the attacker first ran "Whoami" to identify their privilege level, then "ls" to orient themselves, the "uname" to identify the operating system and kernal version. Finally,"cat /etc/passwd" and "cat /etc/shadow" to harvest credentials. The Biggest problem is all the commands are executed by the server successfully and return the output at state of 200.
<br>
<br>

<img width="599" height="288" alt="11" src="https://github.com/user-attachments/assets/4101de53-181a-40bc-96dc-b29e5562f56a" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; From Logs the Traffic is Malicious.
<br>
<br>

<img width="601" height="246" alt="12" src="https://github.com/user-attachments/assets/0aafcf47-052b-40fc-aa9c-ef049697e0a8" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; By seeing the Logs, we can see that the attacker use a classic command injection attack.
<br>
<br>

---
***Endpoint Investigation:***

<img width="596" height="357" alt="13" src="https://github.com/user-attachments/assets/b19ee5c8-272c-43b0-831e-5aea61941faa" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp;In this case, there is no evidence of any planned test. This is Not Planned.
<br>
<br>

<img width="592" height="273" alt="14" src="https://github.com/user-attachments/assets/5d3db346-4576-4523-bb40-59a7bdf4a6a0" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; let's check with the Endpoint security.
<br>
<br>

<img width="1031" height="561" alt="15" src="https://github.com/user-attachments/assets/7e9afb75-3593-433b-b4bc-34191bccf42d" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The traffic direction is Internet -> Company Network. An external attacker at 61.177.172.87 was sending commands to an internal web server.
<br>
<br>

<img width="589" height="468" alt="16" src="https://github.com/user-attachments/assets/f3ef6571-d8f1-472c-a1ae-286c74b48dfc" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Now, let's check on the Terminal History tab on Hostname Webserver1004,
<br>
<br>

<img width="721" height="406" alt="17" src="https://github.com/user-attachments/assets/e1672248-5dc7-4ab8-804f-07b3fcc2cee3" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The terminal historyshows the the commands are executed on the server during the attack. These timestamps match perfectly with the HTTP log entries we reviewed.
<br>
<br>

<img width="594" height="228" alt="18" src="https://github.com/user-attachments/assets/ca1da1f3-fe09-4c4a-b241-2739e31e4b87" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The attack was successful.
<br>
<br>

<img width="594" height="410" alt="19" src="https://github.com/user-attachments/assets/7c7a3b6b-52e7-4a68-95c9-8d9f452ed8af" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Because the device is confirmed compromised and the attack was successful, we need to isolate "Webserver1004" immedialty.
<br>
<br>

<img width="761" height="476" alt="20" src="https://github.com/user-attachments/assets/db8322a8-36ea-461e-920d-22549e2b023d" />

---
***Tier 2 Escalation:***

<img width="590" height="449" alt="21" src="https://github.com/user-attachments/assets/ce46f295-cb1f-420a-b5a6-4ab72728b096" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Because the attack was successful, we need Tier 2 Escalation
<br>
<br>

---
***Documertation:***

<img width="588" height="392" alt="22" src="https://github.com/user-attachments/assets/a6e2f155-6a28-404d-8823-b7cb8d5aa98e" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The reason you document the URL, domain and IP separately is that the URL could change while the IP and domain stays the same. These three gives future analysts more options to detect related activity. The Analyst Note is your chance to tell the full story of what happened in plain language.
<br>
<br>

<img width="591" height="333" alt="24" src="https://github.com/user-attachments/assets/eb4fad25-d689-4d4b-9140-4886dabedcb2" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The attack was successful and the host has been contained. Close case as True Positive.
<br>
<br>

---
<h3 align =center> Summary </h3>

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; An external attacker at 61.177.172.87 exploited a command injection vulnerability in the web application hosted on WebServer1004 (172.16.17.16). By sending HTTP POST requests with OS commands embedded in the ?c= parameter, the attacker successfully ran ls, whoami, uname, cat /etc/passwd, and cat /etc/shadow on the server. Every request was permitted by the firewall, and every command was confirmed executed in the terminal history. The growing HTTP response sizes and the 200 status codes across all requests confirmed the attack was not just attempted but fully successful. The host was contained to stop further access.
<br>
<br>



