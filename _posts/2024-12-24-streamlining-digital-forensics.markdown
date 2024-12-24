---
layout:	post
title:	"Streamlining Digital Forensics Investigations with Cyber Triage"
date:	2024-12-24
tags: Digital-Forensics
image: '/images/medium/hallowen.gif'
imagehero: true
---

In this blog, we delve into the practical application of <b>Cyber Triage</b> tools in analyzing a host compromised by the infamous <b>WannaCry ransomware</b>. Cyber Triage, a powerful incident response tool, simplifies the process of collecting, analyzing, and correlating digital evidence to identify the root cause and impact of an attack.

By leveraging its advanced capabilities, investigators can streamline forensic workflows and enhance the efficiency of their investigations. Visit [Cyber Triage's official page](https://www.cybertriage.com/) to learn more or evaluate the tool. This blog provides actionable insights into how Cyber Triage can be a game-changer in forensic investigations, making it a valuable resource for DFIR professionals and cybersecurity enthusiasts.

Cyber Triage is essential in incident response, particularly when dealing with high-impact ransomware like WannaCry. It enables investigators to rapidly collect and analyze evidence, pinpointing compromised systems and detecting malicious activities. In incidents like WannaCry, where speed is crucial to mitigate damage, Cyber Triage helps quickly identify the attack's entry point, affected systems, and the scope of encryption or data exfiltration. By streamlining the forensic process, Cyber Triage provides critical insights, allowing security teams to respond efficiently and effectively.

View of compromised host that has been acquired

![alt text](/images/image-3.png)

### Basic Host Information

Windows 7 Ultimate - version 6.1 (Build 7601) was installed
![alt text](/images/image-4.png)

We can see that Windows Defender Startup is set to Manual & Firewall is not enabled and indicated with score Suspicious 
![alt text](/images/image-5.png)
![alt text](/images/image-6.png)

### Initial Access

This image shows how Cyber Triage helps identify the point of entry used by WannaCry. The presence of suspicious connections or exploit attempts, like the EternalBlue vulnerability, is highlighted here.
![alt text](/images/image-7.png)

With <b>EternalBlue</b> threat actor exploit the system with the suspicious binary
![alt text](/images/image-8.png)

We can see the timeline threat actor get in to system
![alt text](/images/image-9.png)

### Execution

They created startup item/shedule task for their act on path
/Windows/temp/tasksche.exe
![alt text](/images/image-15.png)

Threat actor stored their tools on directory C:\Windows\Temp this commonly by threat actor because globally writeable. They used <b>sliver</b> as a command control
![alt text](/images/image-10.png)

This image shows processes associated with the ransomware. Cyber Triage identifies abnormal file and process behavior, helping investigators trace the execution phase of WannaCry
![alt text](/images/image-11.png)

### Command & Control

Timeline for threat actor created C2 malware on victim host and with filename and path 11:22:05am EDT September 14, 2023
```python
/windows/temp/chr0me.exe
```

![alt text](/images/image-12.png)

192.168.1.209 is the call-back IP address for the C2 malware.
Here, Cyber Triage helps detect communication between the compromised host and the attacker's C2 server. Anomalous outbound network traffic or suspicious DNS requests often indicate C2 activity.
![alt text](/images/image-13.png)

### Impact

```python
/windows/temp/urb0rk3d.exe
```  
This image illustrates the encrypted files on the host. Cyber Triage helps investigators quickly recognize the encrypted data, assisting in the assessment of the attack’s full impact.
![alt text](/images/impacted-host.png)

Once deployed, WannaCry encrypted crucial files, making them inaccessible to the user. It’s important to note how Cyber Triage helped investigators quickly identify the malware's presence and scope, enabling rapid containment and reducing the potential for data loss.

WannaCry ransomware incident emphasizes the importance of using advanced tools like Cyber Triage in cybersecurity investigations. Key takeaways include the need for regular patching to close vulnerabilities such as MS17-010, the value of real-time analysis tools, and the effectiveness of Cyber Triage in speeding up the response and recovery process. For organizations aiming to improve their cybersecurity posture, adopting proactive threat detection and response strategies like these is critical in defending against future threats.

### Indicators of Compromise

| IP address |
|------------|
| 128[.]31[.]0[.]39
| 149[.]202[.]160[.]69
| 46[.]101[.]166[.]19
| 91[.]121[.]65[.]179

| Domains |
|---------|
| hxxp://www[.]btcfrog[.]com/qr/bitcoinpng[.]php?address
| hxxp://www[.]rentasyventas[.]com/incluir/rk/imagenes[.]html
| hxxp://www[.]rentasyventas[.]com/incluir/rk/imagenes[.]html?retencion=081525418
| hxxp://gx7ekbenv2riucmf[.]onion

| Hashes |
|--------|
|5a89aac6c8259abbba2fa2ad3fcefc6e
| 05da32043b1e3a147de634c550f1954d
| 8e97637474ab77441ae5add3f3325753
| c9ede1054fef33720f9fa97f5e8abe49

[More IoCs](https://github.com/sophoslabs/IoCs/blob/master/Worm-WannaCry)