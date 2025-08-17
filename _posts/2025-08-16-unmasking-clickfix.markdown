---
layout:	post
title:	"When “I’m Not a Robot” Becomes Malware — The ClickFix Deception Unmasked"
date:	2025-08-16
tags: Malware-Analysis
image:  '/images/medium/clickfix.gif'
imagehero: true
---

In the evolving world of cybersecurity threats, *ClickFix* is one of the clearest examples of why people are often the weakest link in the security chain.

Unlike sophisticated zero-day exploits that target software flaws, ClickFix manipulates human trust in familiar web elements like CAPTCHAs, video conferencing login pages, and “Fix it” prompts. By mimicking familiar elements like CAPTCHAs and “Fix it” pop-ups, ClickFix tricks users into executing harmful code themselves.

From late 2024 into 2025, security teams from various vendor or intelligence team like **Splunk**, **Darktrace**, **Unit42**, **Sekoia** or whatever have all documented the rise of ClickFix campaigns. These attacks have been observed delivering:

- **Lumma Stealer** and other credential theft malware
- **Remote Access Trojans (RATs)** for spying and persistence
- **Ransomware** like *Epsilon Red*, delivered via malicious `.HTA` files

It’s a reminder that **sometimes the attacker doesn’t need to break in — they just need you to open the door yourself**.

# How ClickFix Actually Works?
![alt text](/images/flow.png)

### **1. The Lure**
The victim encounters a link through:<br>
- Phishing emails
- Social media messages
- Malicious advertisements (*malvertising*)
- SEO poisoning — malicious sites ranked highly for certain keywords

These links direct victim to clicks a seemingly legitimate link to a **compromised or attacker-controlled website**.

### **2. The Fake Verification**
The site presents a believable interface — maybe a CAPTCHA with **“I’m not a robot”** or a fake **“Your meeting can’t start”** screen.

A realistic-looking fake CAPTCHA or error message appears. It looks normal, but under the hood, it’s scripted to **silently copy** a hidden payload or command to the clipboard.

### **3. The Instruction**
The page then displays a message such as:

 **“Please press Windows + R, paste the code, and press Enter to continue.”** 

This instruction is crafted to feel urgent and routine and it will be copies a malicious command to the clipboard then the victim will run it.

### **4. The Execution**
When the user pastes and runs the code, it triggers a **living-off-the-land binary (LOLBIN)** like `mshta.exe` or `powershell.exe` to fetch and execute the malware from a remote server.

### **5. The Payload**
Depending on the campaign, this payload may:<br>
- Install a stealer to harvest credentials, crypto wallets, and browser data
- Deploy a RAT for long-term access
- Execute ransomware to encrypt files and demand payment

Nation-state actors such as Iran-linked MuddyWater and Russia-linked APT28 have adopted the ClickFix technique in their cyber espionage campaigns.

# Why ClickFix Is So Effective

- **Familiarity Breeds Trust**
<br>Most users have seen CAPTCHAs and verification steps countless times. Attackers piggyback on this familiarity to lower suspicion.

- **Low Technical Barrier for Attackers**
<br>No advanced exploit is needed; just a convincing web page and social engineering.

- **Bypasses Many Security Tools**
<br>Because the user initiates execution, security software may treat the action as “legitimate.”

- **Leverages Built-in Windows Tools**
<br>By abusing mshta and powershell, attackers blend malicious activity with normal system operations.

# Real Campaign Examples

- **Fake reCAPTCHA**
<br>This variant closely mirrors the look and functionality of the legitimate Google reCAPTCHA, making it highly convincing to internet users.

![image.png](/images/mega77.png)

- **Fake Meeting Pages** 
<br>Victims were sent “Google Meet” or “Microsoft Teams” invites with instructions to verify their meeting by running a code used by Kimsuky (North Korea), MuddyWater (Iran), UNK_RemoteRogue, and APT28 (Russia).

- **Fake Browser Updates**
<br>Malicious sites displayed “Update Required” pop-ups that copied a PowerShell one-liner to the clipboard used by SocGholish, BitRAT, Lumma Stealer, and FrigidStealer (macOS)

- **Ransomware via HTA** 
<br> ```.hta``` payloads delivered *Epsilon Red* ransomware directly after execution.

- **Cloudflare Bot Protection**
<br>Another variation of the ClickFix technique is Cloudflare bot protection. Several phishing sites have been identified that imitate well-known brand sites, only to redirect users to a ClickFix page.

![image.png](/images/twitch.png)


# ClickFix Mitigation & Detection

### Mitigations **For Organizations**

- Harden **PowerShell execution policies** and log encoded command usage.
- Apply GPO restrictions to disable or limit **Windows+R** usage, reducing exposure to Run dialog abuse.
- Restrict or block `wscript.exe` and `mshta.exe` using **AppLocker** or **Windows Defender Application Control (WDAC)**.

### **User Protection & Awareness**

- Never paste or run commands you didn’t request. Treat unexpected prompts as malicious.
- Legitimate CAPTCHAs never require Windows commands. Report any verification asking for **Win+R** usage.
- Be cautious of **social engineering lures** such as:
    - Fake “urgent” browser update pop-ups.
    - Meeting invites that require scripts or command execution.
- Update OS, browsers, and security tools only from official sources, not from pop-ups.
- Use established reporting procedures for suspicious websites or prompts.

### **Detection Opportunities**

**1. Clipboard-to-Process Chains**
<br>Alert when clipboard activity is followed by suspicious process creation (e.g., browser → paste → `powershell.exe` or `mshta.exe`).

**2. Script & LOLBIN Abuse**
<br>
- Monitor or block `.hta` file execution.
- Flag `mshta.exe` or `powershell.exe` making outbound connections.
- Detect **Base64-encoded PowerShell** or suspicious `IEX` (Invoke-Expression) usage.
- Watch for AMSI bypass attempts containing strings like `AMSI_RESULT_NOT_DETECTED` (linked to Lumma Stealer and other ClickFix malware).

**3. Web & Process Correlation**
<br>Correlate visits to untrusted domains (e.g., fake updates or meeting sites) with new process launches.

**4. RunMRU Artifacts**
<br>As a Digital Forensic and Incident Responder we can check on RunMRU. Windows maintains a registry key that stores the most recently executed commands from the **Run window (Win + R)**, called RunMRU.
- Monitor `RunMRU` registry entries:

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
```

- RunMRU is not persistence, but a strong forensic indicator of ClickFix activity.
<br>With that in mind, let’s look at what a successful ClickFix attack looks like on an endpoint. We’ll start by opening the Run dialog (Windows + R) and pasting in a ClickFix command from earlier.

![image.png](/images/win+r.png)

The execution of the command is tracked using Process Monitor

![image.png](/images/procmon-clickfix.png)

Inspecting the registry write operation (RegSetValue), we find the entire PowerShell command is written to the RunMRU registry key.

![image.png](/images/runmru.png)

**5. Alternate Invocation (Win+X Abuse)**

- Some attackers avoid RunMRU exposure by instructing victims to use 
<br> **Win+X → PowerShell/Command Prompt**.
- Hunt for:
    - **Event ID 4688 (Process Creation):
    <br> ** `powershell.exe` spawned by `explorer.exe`.
    - **Event ID 4663 (Object Access):
    <br> ** file activity under `%LocalAppData%\Microsoft\Windows\WinX\`.
    - Elevated PowerShell sessions shortly after login, followed by suspicious child processes 
    <br> (`certutil.exe`, `mshta.exe`, `rundll32.exe`).
    - Clipboard paste events correlated with PowerShell execution.

# Threat Hunting ClickFix

For the hunting on our environment, we must ensure we have the appropriate event logs from specified sources like command line auditing has been enabled and sysmon.
<br> One of the way to detect the activity, the following query can be used to hunt for suspicious PowerShell command strings

**Suspicious PowerShell Parameter Substring Detected**

```sql
label="Process" label=Create      
"process" IN ["*\powershell.exe", "*\pwsh.exe"]     
command IN ["* -wi*h*", "* -nopr*", "* -nonin*", "* -ec*", "* -en*", "* -executionp*", "* -e* bypass*",
"* -sta *","*FromBase64String*", "*irm*iex*", "Invoke-RestMethod*Invoke-Expression*","*Convert-String*"]        
```

For more detections we can use [detection.ai](http://detection.ai) one of the best community platform for detection engineering with specific ClickFix detections

![image.png](/images/detectionsai.png)
![image.png](/images/detectionsaii.png)
![image.png](/images/detections-ai.png)

# Hunting ClickFix Infrastructure

We can leverage FOFA, internet asset search engine that continuously scans the IPv4, IPv6, and domain space, indexes banners.  With these querry we can found malicious web indication with ClickFix in the wild.

```sql
body="In the verification window, press <b>Ctrl</b>"
```

![image.png](/images/fofa.png)

To validate those website indication with ClickFix we can use ClickGrab tools. ClickGrab Analyzer is a tool designed to identify and analyze websites that may be using FakeCAPTCHA or ClickFix techniques to distribute malware or steal information. It analyzes HTML content for potential threats like PowerShell commands, suspicious URLs, and clipboard manipulation code.

![image.png](/images/clickgrab.png)

![image.png](/images/clickgrab-analysis.png)

We can checked the detail like the URLs, IP Address, Powershell Command and Obfuscated Javascript etc

![image.png](/images/clickgrab-detail.png)

Another way we can checked manually by visiting the website

![image.png](/images/sindangkasihnews.png)

or the others website we can check on FOFA

![image.png](/images/capazmente.png)

![image.png](/images/threat-score.png)

Show on image that website IEX to download malicious VBS 

# Conclusion: The Real Exploit Is Human Behavior

ClickFix’s most dangerous payload isn’t code — it’s **psychology**. These campaigns succeed by exploiting habits, trust, and the instinct to follow “official-looking” instructions, turning the victim into an active participant in their own compromise.

Technical delivery is almost secondary; the human response is the true entry point. The strongest defense isn’t just better software — it’s awareness. If a site ever asks you to run or paste a command, **close it immediately**. No legitimate service will require that. The weakest link isn’t hardware — it’s human behavior

*Think before you click — and never paste what you didn’t type yourself.*