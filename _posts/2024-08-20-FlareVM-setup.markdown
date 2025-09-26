---
layout:	post
title:	"Malware Analysis & Reverse Engineering Cheat Sheet"
date:	2024-08-20
tags: Malware-Analysis
image: '/images/medium/3.png'
imagehero: true
---

Cheat sheet for building a local, isolated sandbox for malware analysis and reverse engineering. This concise guide walks you through setting up with Windows 10 VM, installing and configuring FLARE-VM, and applying network isolation best practices. With step-by-step instructions and screenshots, you'll have a secure lab to practice malware analysis and improve your reverse-engineering skills.

# 1. Malware Analysis Process

## Behavioural Analysis
Use virtualisation tools for system snapshots:
- Clonezilla
- PXE
- FOG

Monitor local interactions:
- Process Hacker
- Process Monitor
- ProcDOT
- Noriben

Detect system changes:
- RegShot
- Autoruns

Monitor network traffic:
- Wireshark
- Fiddler

Redirect traffic:
- fakedns
- accept-all-ips

Simulate services:
- INetSim
- actual service setup  

## Ghidra for Static Code Analysis

| **Action** | **Shortcut** |
|---|---|
| Go to location	 | `g` |
| Show references	 | `Ctrl+Shift+F`|
| Insert comment	| `;` |
| Follow jump or call	 | `Enter` |
| Previous/Next location | `Alt+Left / Alt+Right` |
| Undo | `Ctrl+Z` |
| Define data type | `t` |
| Add bookmark | `Ctrl+D` |
| Text search | `Ctrl+Shift+E` |
| Add/edit label | `l` |
| Disassemble | `d` |


## x64dbg/x32dbg for Dynamic Code Analysis

| **Action** | **Shortcut** |
|---|---|
| Run code | `F9` |
| Step into / over	 | `F7 / F8`|
| Execute until instruction	| `F4` |
|Execute until return		 | `Ctrl+F9` |
| Previous/Next location | `- / +` |
| Return to previous view	 | `*` |
| Go to expression	 | `Ctrl+G` |
| Comment / Label	 | `; / :` |
| Show current function graph	 | `g` |
| Set breakpoint (instruction/API)	 | `F2 / SetBPX APIName` |
| Highlight occurrences	 | `h` |
| Assemble instruction | `Spacebar` |
| Edit data in memory	| `Ctrl+E` |
| Extract API call references | `Right-click → Search for → Current module → Intermodular calls` |

## Unpacking Malicious Code
Detect packing: 
- Detect It Easy (DIE)
- Exeinfo PE 
- Bytehist 
- peframe

Precise unpack:
- Find OEP (Original Entry Point) via debugger
- Use `OllyDumpEx`
- Set breakpoints on APIs: `LoadLibrary`, `VirtualAlloc`
- Use memory breakpoints at stack entry

Rebuild dumped file
- Scylla
- pe_unmapper

## Bypassing Other Analysis Defences
Decode obfuscated strings:
- FLOSS
- xorsearch
- Balbuzard

Hide analysis tools: Use ScyllaHide plugin for x64dbg
Watch for tricky control flows
- TLS
- SEH
- RET
- CALL
Use `scdbg` and `runsc` for shellcode
Disable ASLR 
- setdllcharacteristics
- CFF Explorer


# 2. Analyzing Malicious Documents
## Microsoft Office Format Notes
- `OLE2 Format` (.doc, .xls, etc.)

- `OOXML Format` (.docx, .xlsm, etc.)

`XLM Macros`: Excel formulas, even without binary OLE2 stream.

`RTF`: No macros, but supports embedded malicious objects.

## Risky Windows API Calls
Code injection
- `CreateRemoteThread` 
- `WriteProcessMemory`

DLL loading
- `LoadLibrary` 
- `GetProcAddress`

Data theft
- `GetClipboardData`
- `GetWindowText`

Keylogging: 
- `GetAsyncKeyState`
- `SetWindowsHookEx`

Self-injection: 
- `VirtualAlloc` 
- `VirtualProtect`

Execution: 
- `CreateProcess` 
- `WinExec`

Web traffic
- `InternetOpen`
- `HttpSendRequest`


# 3. Get Started with Sandbox
## Windows Malware Sandbox with FLARE

- Use your own Virtual Machine such as [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or [VMWare](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Workstation%20Pro&freeDownloads=true)

- Use Windows 10 from the official website:
[Windows ISO Download Link](https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise)


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

- Download chrome browser for smooth experience

- Download and Install [FlareGitHub Repo](https://github.com/mandiant/flare-vm) or we can use scripting to fastest our way with this [install.ps1](https://github.com/mandiant/flare-vm/blob/main/install.ps1)

Before you run the ps.1 please read carefully the documentaion such as
<br> ```Unblock-File .\install.ps1``` and <br> ```Set-ExecutionPolicy Unrestricted -Force``` 
<br> and then you can execute the installer ```.\install.ps1```

- Change network adapter to `Host-Only` 🖥️

## Linux Malware Sandbox with REMnux
- Install REMnux via VM, dedicated system, or on existing distro
- [Documention](https://docs.remnux.org)
- Keep updated with `remnux upgrade` and `remnux update`
- Use Docker-based tools
- Default login: `remnux/malware`

- Use Docker Containers for Analysis <br>
`1. emnux/thug` <br>
`2. remnux/jsdetox` <br>
`3. remnux/retdec` <br>
`4. remnux/viper` <br>
`5. remnux/radare2` <br>