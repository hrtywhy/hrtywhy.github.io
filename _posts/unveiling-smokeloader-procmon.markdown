---
layout:	post
title:	"Unveiling Smokeloader with Procmon "
date:	2024-02-01
tags: Malware-Analysis
image:  '/images/medium/1.jpg'
imagehero: true
---

Decoding malware loaders using Procmon and AI. Utilising Powershell to retrieve additional payloads and free online tooling to identify the malware family.

This post will discuss how to analyze a smokeloader (.vbs) malware using procmon. The initial file can be downloaded from [malware bazaar](https://bazaar.abuse.ch/sample/375798f97452cb9143ffb08922bebb13eb6bb0c27a101ebc568a3e5295361936/) and unzipped using the password _infected_. The malware file after unzipping is a VBS Script.

![malware](image.png)