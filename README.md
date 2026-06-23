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
&nbsp;&nbsp;&nbsp;&nbsp; The rule triggered because JavaScript code was detected inside the requested URL. Specifically, look at the URL:









