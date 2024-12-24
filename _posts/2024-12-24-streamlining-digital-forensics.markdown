---
layout:	post
title:	"Streamlining Digital Forensics Investigations with Cyber Triage"
date:	2024-12-24
tags: Digital-Forensics
image: '/images/medium/hallowen.gif'
imagehero: true
---

In this blog, we delve into the practical application of <b>Cyber Triage</b> tools in analyzing a host compromised by the infamous WannaCry ransomware. Cyber Triage, a powerful incident response tool, simplifies the process of collecting, analyzing, and correlating digital evidence to identify the root cause and impact of an attack.

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

As we can see threat actor utilize RCE vulnerabilities MS17–010 / CVE-2017-0144 (EternalBlue)
![alt text](/images/image-7.png)

With EternalBlue threat ector exploit the system with the suspicious binary
![alt text](/images/image-8.png)

We can see the timeline threat actor get in to system
![alt text](/images/image-9.png)

### Execution

They created startup item/shedule task for their act on path
/Windows/temp/tasksche.exe
![alt text](/images/image-15.png)

Threat actor stored their tools on directory C:\Windows\Temp this commonly by threat actor because globally writeable

They used sliver as a command control
![alt text](/images/image-10.png)

With this tools C2 they installed ransomware
![alt text](/images/image-11.png)

### Command & Control

Timeline for threat actor created C2 malware on victim host and with filename and path 11:22:05am EDT September 14, 2023

```python
/windows/temp/chr0me.exe
```

![alt text](/images/image-12.png)

192.168.1.209 is the call-back IP address for the C2 malware


![alt text](/images/image-13.png)

### Impact

```python
/windows/temp/urb0rk3d.exe
```  
is the path and filename for the ransomware

![alt text](/images/impacted-host.png)

The WannaCry ransomware attack highlights the importance of timely digital forensics in incident response. Cyber Triage plays a pivotal role in this process, offering streamlined tools to identify compromised systems, track the spread of malware, and mitigate its impact. Key takeaways include the necessity of keeping systems updated to prevent vulnerabilities, the value of real-time analysis tools like Cyber Triage, and the importance of rapid isolation and containment during such attacks. Adopting these best practices can significantly enhance an organization's ability to defend against future cyber threats.
