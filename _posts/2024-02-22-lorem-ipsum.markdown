---
layout:	post
title:	"Creating an Isolated Lab Environment for Malware Analysis & Reverse Engineering"
date:	2024-08-20
tags: Malware-Analysis
image: '/images/medium/3.png'
imagehero: true
---

Welcome to my guide on creating a local sandbox an isolated lab environment for malware analysis and reverse engineering. This step-by-step tutorial covers VirtualBox setup, creating Windows-10 VM, FLARE-VM configuration and network isolation. With clear instructions and screenshots, you’ll have a secure environment for honing your cybersecurity skills and analyzing malware effectively.

- Download and Install Virtual Box:
[VirtualBox Download Link](https://www.virtualbox.org/wiki/Downloads)

- Download Windows 10 from the official website:
[Windows ISO Download Link](https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise)

After import the iso select the custom installation option. 
- Insert and install VirtualBox Guest Addition

![image](https://github.com/user-attachments/assets/47f67fc8-b936-4122-bc36-42b0a88a3bcb)


Restart windows for the changes we do

- Disable Windows Update

go to *services.msc*

![Screenshot 2024-08-19 231137](https://github.com/user-attachments/assets/09d750eb-33e9-44e6-bb7a-441eda19f0e2)

![Screenshot 2024-08-19 231414](https://github.com/user-attachments/assets/f3dae957-9095-48d1-9dea-05c38f96be1c)

- Disable Windows Defender

![Screenshot 2024-08-19 231627](https://github.com/user-attachments/assets/e7639b08-47e6-4f50-9c71-9fb94dc5eb37)

and then go to *gpedit.msc*

![Screenshot 2024-08-19 231742](https://github.com/user-attachments/assets/d5997bae-25e8-4a35-b606-a0ae789aaebd)

Administrative Templates -> Windows Component -> Microsoft Antivirus Defender -> Real Time Protection. Enable *Turn off-real time protection* 

![Screenshot 2024-08-19 232130](https://github.com/user-attachments/assets/23addc52-3800-434a-a41b-edef7bbedb55)

Set the same things for Microsoft Defender Antivirus 

![Screenshot 2024-08-19 232219](https://github.com/user-attachments/assets/258d3e85-5473-4184-8f4a-a16ccb27d71d)

Dont forget to reboot!

- Show Hidden Files and Folders
  
![Screenshot 2024-08-19 233722](https://github.com/user-attachments/assets/0a73f5e4-2206-4f81-9e20-1c8f3e44ba5c)

- Create a snapshot

FLARE-VM is a purpose-built virtual machine created & maintained by FireEye, a cybersecurity company. It comes pre-configured with a variety of tools, software, and scripts commonly used for malware analysis and reverse engineering tasks. These tools include disassemblers, debuggers, memory analysis tools, and various utilities for analyzing and dissecting malware samples.
It provides a controlled and isolated environment for security analysts to safely analyze potentially malicious software without risk to their own systems. It’s a valuable resource for those working in the field of cybersecurity and malware analysis to better understand and defend against threats.

[Flare-VM GitHub Repo](https://github.com/mandiant/flare-vm)

download chrome for smooth experience and download flare

Copy link address for [install.ps1](https://github.com/mandiant/flare-vm/blob/main/install.ps1)

follow the installation setup on documentation such as
```Unblock-File .\install.ps1``` and ```Set-ExecutionPolicy Unrestricted -Force``` and then you can execute the installer ```.\install.ps1```

dont forget to change network adapter on VirtualBox to Host Only

Flare VM setup completed! 🖥️


