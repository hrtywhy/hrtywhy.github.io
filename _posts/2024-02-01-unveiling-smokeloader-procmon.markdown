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

![malware](/images/vbs.png)

We can see the source code of VBS Script using an editor, here I use notepad++, other text editors such as sublime, visual studio code or other editors can also be used.

![notepad](/images/notepad.png)

It can be seen that the source code only has 10 lines and is not human readable or cannot be read at all.

Then we open procmon, a tool from Sysinternals. This method will capture any new processes spawned by the obfuscated script, revealing any decoded command line arguments that were used to spawn the new process. This bypasses a lot of the obfuscation that may be present in an original encoded script.

Procmon can capture hundreds or even thousands of events per second. This can quickly consume memory, so you need to ensure that you capture the right events.
1.	Open procmon
2.	Stop capturing (CTRL+E) 
3.	Delete the window (CTRL+X)
4.	Set the wscript.exe filter 
5.	Then restart capture and run the malware

We can see that there are thousands of events captured by procmon. Stop capture (CTRL+E)

![procmon](/images/procmon.png)