---
layout:	post
title:	"Deep Dive Into Redline Stealer"
date:	2024-02-19
tags: Malware-Analysis
image: '/images/medium/2.jpg'
imagehero: true
---

RedLine is a stealer distributed as cracked games, applications, and services. The malware steals information from web browsers, cryptocurrency wallets, and applications such as FileZilla, Discord, Steam, Telegram, and VPN clients. The binary also gathers data about the infected machine, such as the running processes, antivirus products, installed programs, the Windows product name, the processor architecture, etc. The stealer implements the following actions that extend its functionality: Download, RunPE, DownloadAndEx, OpenLink, and Cmd. The extracted information is converted to the XML format and exfiltrated to the C2 server via SOAP messages.

The initial file can be downloaded from [malware bazaar](https://bazaar.abuse.ch/sample/3086ac8861aaccdf3dc45f3b1380b6cd70169c7d9fc16f098f5a1d08736fed61/) and unzipped using the password infected
