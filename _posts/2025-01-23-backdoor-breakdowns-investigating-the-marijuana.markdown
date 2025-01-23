---
layout:	post
title:	"Backdoor Breakdowns Investigating the Marijuana"
date:	2025-01-23
tags: Malware-Analysis
image:  '/images/medium/marijuana.png'
imagehero: true
---

The beginning of 2025 was quite hectic for me as I assisted a friend whose business operates in the media industry. My friend complained about Chinese ads appearing on their website and, upon further investigation on Google, discovered Chinese and Turkey's languange backlinks directing users to their site. These backlinks are often exploited by actors such as "slot gacor" operators to boost their search rankings on Google, embedding links on other websites to improve their visibility and achieve first-page results.

# Case Summary

The Marijuana PHP shell operates as a backdoor, granting threat actors initial access to compromised servers while remaining undetected. This malware is specifically engineered to bypass Web Application Firewalls (WAFs) by using hexadecimal obfuscation for incoming requests. The obfuscation masks the malicious payloads, making detection and prevention by automated security systems significantly more challenging.

Once executed, the Marijuana PHP shell provides attackers with a browser-accessible interface, enabling comprehensive file management capabilities such as:

Uploading and downloading files.
Modifying file permissions.
Viewing and editing server files.
This functionality allows threat actors to establish persistence, gather sensitive information, and prepare for privilege escalation or further exploitation. Despite its potential impact, the PHP shell is designed to operate covertly without disrupting the website’s functionality, leaving website owners unaware of its presence until specific signs of compromise emerge.

# Initial Access

Based on the logs I analyzed, I am highly confident that the threat actor gained initial access through exploit Public-Facing Application that identified by vulnerability in the CMS or its plugins. During this intrusion, the attacker utilized a request to upload a malicious folder named <b>"bk,"</b> which contained additional malicious files such as `geju.php`, `hiroshi.php`, and `godsend.php`.

![alt text](/images/logz.png)

# Execution

Threat actor utilize PHP backdoor web shell known as <b>Marijuana</b>. Marijuana can be used as a file manager accessed through a web browser. The shell provides a threat actor the primary ability to read and write content on the host, amongst other actions.

The author of the Marijuana PHP shell has a GitHub page which claims that this is a stealth backdoor encoded with hex to bypass web application firewalls (WAF's). The screenshot of the GitHub repository below highlights the features that this shell is allegedly equipped with such as file download and CHMOD. These features are investigated further in this article.

![alt text](/images/marjun.png)
![alt text](/images/github-marjun.png)

The following image shows the threat actor have a access all about the server.<br>
hxxps://domain[.]com/bk/index.php

![alt text](/images/marijuana.png)
 
In the folder will create some malicious files like index.php, cache.php and .httaccess

![alt text](/images/marjun-cache.png)

The index.php file is largely encoded in hexadecimal, a method used to evade Cloudflare's WAF. The MARIJUANA shell employs selective obfuscation by encoding PHP functions commonly flagged as suspicious or associated with malware. Instead of plaintext usage, these functions are obfuscated as hexadecimal values stored in an array

Threat Actor's frequently employ code obfuscation techniques to evade signature-based scanners and other security measures. In some cases, the entire file’s code is obfuscated, while in others, only specific sections are targeted for obfuscation.

![alt text](/images/array.png)

Decode the array from hexadecimal format into ASCII characters. Refer to the image provided for more details. In the image, PHP functions begin to appear, but they are concatenated into a single, continuous string.

The “$GNJ” variable on bottom which is decoded using a custom “UHEX” function. This variable is later used by referencing indexes in the array which call upon functions, this is the logic that allows the function calls to bypass Cloudflare WAF. This variable also i use for scanning on the environtment infected.

![alt text](/images/ascii.png)
![alt text](/images/code.png)

That web shell provides a range of features and functions that can be used by attackers for various purposes. Those functions include file manipulation (uploading, renaming, removing), execution of commands (such as chmod and unzip), and manipulating file timestamps.

There is interesting function with name <b>htmlspecialchars</b>, and <b>file-get-contents</b>. These functions are used together to read the contents of a file. The function is designed to allow a GET request to encode both the file and its path. Parameters such as `d` (referencing the directory) and `s` (referencing the file name) are used to construct a URL request.

The primary reason for this encoding is to obfuscate the request, making it difficult for Cloudflare to identify and flag it as suspicious.

![alt text](/images/codes.png)

We can see the artifacts in these logs.

![alt text](/images/bk-log.png)

To identify this type of malicious behavior, one option is to use a tool like [CyberChef](https://gchq.github.io/CyberChef/) to quickly convert the hexadecimal values above, helping you to determine that the request is trying to view the file index.php from within the directory under `/home/[redacted]/public_html/adv`

![alt text](/images/ascii-adv.png)
another file with name `cache.php` and this indicate a shell flagged on VirusTotal

![alt text](/images/codess.png)
![alt text](/images/Vtot.png)

# Persistence

After some deep dive analyzing thru `cache.php` its just only cache file of the backdoor, but the others malicious file to create persistence.

![alt text](/images/codesszz.png)

From the User-Agent we know that is malware and and there is authkey

![alt text](/images/coder.png)

I dont have any idea about this, but it seems function to check if any files with those specific format and it will be embed a malicious code or backdoor. `encodePath()` and `decodePath()` functions indicate intentional obfuscation of directory paths to bypass detection.

![alt text](/images/coderx.png)


The same malicious code with different name `wp-conflg.php` . The file `wp-config.php` is a legitimate core configuration file for WordPress, critical for the website’s functionality. However, the malicious file `wp-conflg.php` is an impersonation attempt where the attacker has replaced the letter <b>"i"</b> with <b>"l"</b> to evade detection. This subtle change can deceive admins into believing the file is legitimate and avoid deleting it, even though it is a malicious script designed to compromise the system.

![alt text](/images/pw-name.png)

As i said before there is function to embed malicious code to specific extension and yups i found it on both of these files

![alt text](/images/files-embed.png)

Upon checking, based64 encode script embeded on file 

![alt text](/images/base64.png)

To evading detection, they obfuscated 3x 

![alt text](/images/flow-graph.png)

Furthermore, bk folder contains malicious files will be create on each folder on the server. Those malicious files identified infected and created more than 4K files on the server

![alt text](/images/infected-files.png)

 Also i found the similar content like this [file](https://pastebin.com/YH9zHMCp) belong to marijuana file encoded with decimal. Unfornutely, I didn't save it as an artifact before I deleted it.

# Defense Evasion

By analyzing the malicious files, i notice multiple defense evasion functionalities implemented, such as:
- Bypasssing WAF <br>
<i>The shell possesses a “stealth” mode, which can be used to bypass website security services like web application firewalls with encoded functions to hex for bypassing WAF</i>

- Embedded Payloads

- Command Obfuscation

# Impact

During the intrusion, no final actions beyond data collection and discovery tasks were observed.


# Diamond Model

![alt text](/images/diamond-model.png)

# Indicators

| IOC |
---------------
| h1xxp://zs230[.]seboit[.]skin |
| 162.158.78.254 |
| 188.114.97.1 |
| 172.70.135.75 |
| 172.70.130.253 |
| 63.141.247.162 |


