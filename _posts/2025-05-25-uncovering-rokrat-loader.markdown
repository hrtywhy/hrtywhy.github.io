---
layout:	post
title:	"Uncovering the Marijuana PHP Backdoor and How They Evading Detection"
date:	2025-01-23
tags: Malware-Analysis
image:  '/images/medium/marijuana.png'
imagehero: true
---

At the start of 2025, I found myself navigating a challenging situation while assisting a friend in the media industry. They reported unusual activity on their website, including unsolicited Chinese advertisements. Upon deeper investigation, we identified backlinks in Chinese and Turkish, redirecting traffic to their site. These backlinks were likely being exploited by threat actors—commonly associated with “slot gacor” operations—to manipulate search rankings on Google. By embedding these links across compromised websites, they aimed to boost their visibility and secure top search engine results.

# Case Summary

The Marijuana PHP Backdoor is an advanced malicious script designed to infiltrate and compromise web servers. Leveraging vulnerabilities and sophisticated obfuscation techniques, it operates stealthily, evading detection by security systems and posing a significant threat to online platforms. The obfuscation conceals malicious payloads, complicating detection and mitigation efforts by automated tools.

Upon execution, the Marijuana PHP shell grants attackers a browser-accessible interface equipped with extensive file management features, including:

- Uploading and downloading files.
- Modifying file permissions.
- Viewing and editing server-side files.

This capability allows threat actors to maintain persistence, exfiltrate sensitive information, and prepare for privilege escalation or further exploitation. Despite its potentially severe impact, the PHP shell is crafted to operate covertly, ensuring the website’s functionality remains unaffected. As a result, website owners are often unaware of its presence until clear indicators of compromise are detected.

# Initial Access

From the analyzed logs, it is evident that the threat actor gained initial access by exploiting a vulnerability in the CMS or its plugins—a commonly targeted entry point for public-facing applications. During this intrusion, the attacker uploaded a malicious folder named <b>"bk"</b>, which served as a payload container. This folder housed additional malicious files, including `geju.php`, `hiroshi.php`, and `godsend.php`, further enabling the attacker’s activities.

![alt text](/images/logz.png)

# Execution

Threat actors have leveraged a PHP backdoor web shell identified as Marijuana. This shell functions as a browser-accessible file manager, granting attackers the ability to read, write, and manipulate content on the compromised host, among other capabilities.

The Marijuana PHP shell's creator maintains a GitHub repository that promotes it as a stealthy backdoor designed to bypass web application firewalls (WAFs) by employing hexadecimal encoding. The repository highlights several key features, including file downloads and CHMOD functionality. These capabilities are analyzed in detail later in this article.

![alt text](/images/marjun.png)
![alt text](/images/github-marjun.png)

The following image shows the threat actor have a access all about the victim server.<br>
`hxxps://domain.com/bk/index.php`

![alt text](/images/marijuana.png)
 
The folder contains several malicious files, including `index.php`, `cache.php`, and `.htaccess.`

![alt text](/images/marjun-cache.png)

The `index.php` file is primarily encoded in hexadecimal to bypass Cloudflare’s Web Application Firewall (WAF). The Marijuana shell uses selective obfuscation, encoding commonly flagged PHP functions into hexadecimal values stored within an array. This technique obscures the code to avoid detection by signature-based scanners and security measures


![alt text](/images/array.png)

The hexadecimal array can be decoded into ASCII characters, revealing PHP functions concatenated into a continuous string.

The `$GNJ` variable, decoded using a custom `“UHEX”` function, is used to reference array indexes that invoke obfuscated functions, thus circumventing Cloudflare’s WAF. This variable also aids in scanning the infected environment for further detection.

![alt text](/images/ascii.png)

![alt text](/images/code.png)

The web shell offers various features that attackers can exploit for different purposes, such as file manipulation (upload, rename, delete), command execution (e.g., `chmod`, `unzip`), and altering file timestamps

One notable function is `htmlspecialchars`, used in conjunction with `file-get-contents` to read file contents. These functions allow the encoding of both the file path and name through GET requests, utilizing parameters like `d` (directory) and `s` (file name) to construct the request. This encoding obfuscates the request, making it harder for Cloudflare to detect and flag it as malicious

![alt text](/images/codes.png)

The logs provide clear artifacts that indicate the malicious activity

![alt text](/images/bk-log.png)

To detect this type of malicious behavior, you can use tools like [CyberChef](https://gchq.github.io/) to decode the hexadecimal values. This helps reveal that the request is attempting to access the index.php file within the `/home/[domain]/public_html/adv directory`.

![alt text](/images/ascii-adv.png)

Another file, `cache.php`, was found, which has been flagged as a shell on VirusTotal.

![alt text](/images/codess.png)

![alt text](/images/Vtot.png)

# Persistence

After a deeper analysis of `cache.php`, it became evident that this file is not just a cache for the backdoor, but also part of a network of malicious files designed to maintain persistence on the system.


![alt text](/images/codesszz.png)

The `User-Agent` indicates that this is malware, and it includes an authentication key `(authkey)`.

![alt text](/images/coder.png)

Although the exact purpose is unclear, it appears the `encodePath()` and `decodePath()` functions are used to check for specific file formats and embed malicious code or backdoors. The use of these functions suggests intentional obfuscation of directory paths to evade detection

![alt text](/images/coderx.png)


The malicious file `wp-conflg.php` is an attempt to impersonate the legitimate WordPress configuration file `wp-config.php`. The attacker subtly replaces the letter `"i"` with `"l"` to avoid detection. This small change can deceive administrators into mistaking the file for legitimate, potentially allowing it to remain undetected and maintain control over the system

![alt text](/images/pw-name.png)

As previously mentioned, there is a function designed to embed malicious code into files with specific extensions. I discovered this functionality within both of the identified files

![alt text](/images/files-embed.png)

Upon execution, the obfuscated strings are decoded, triggering the execution of malicious actions

![alt text](/images/base64.png)

To evade detection, the code undergoes three layers of obfuscation

![alt text](/images/flow-graph.png)

Additionally, the <b>`bk`</b> folder contains malicious files that are created in every directory on the server. These files have been identified as part of the infection, leading to the creation of over 4,000 malicious files across the system

![alt text](/images/infected-files.png)

I also discovered a [file](https://pastebin.com/YH9zHMCp) with content similar to the one found in the Marijuana backdoor, encoded in decimal. Unfortunately, I didn’t save it as an artifact before deleting it.

# Defense Evasion

Upon analyzing the malicious files, I identified multiple defense evasion techniques, including:

- Bypassing WAF <br>
The shell has a <i>"stealth"</i> mode that allows it to bypass web application firewalls by encoding functions into hexadecimal to avoid detection.

- Embedded Payloads<br>
Malicious payloads embedded within the files.

- Command Obfuscation<br>
The use of obfuscation techniques to disguise and execute commands without triggering alarms.

# Impact

During the intrusion, no actions beyond data collection and reconnaissance were observed, with no final exploit or system compromise taking place.

# Diamond Model

![alt text](/images/diamond-model.png)

# Indicators

| Domains |
|---------|
| hxxp://zs230[.]seboit[.]skin

| IP address |
|------------|
| 162.158.78.254
| 188.114.97.1
| 172.70.135.75
| 172.70.130.253
| 63.141.247.162

# Conclusion

The Marijuana PHP Backdoor employs advanced evasion tactics, such as obfuscation, payload embedding, and WAF bypassing, making it a significant threat. To protect systems from such attacks, it's crucial to regularly patch vulnerabilities, particularly in PHP-based applications, and implement robust security configurations, such as proper input sanitization and WAF tuning. Additionally, swift identification of indicators and proactive incident response can greatly reduce the impact of these attacks. Staying vigilant and up-to-date with security best practices is essential to defending against similar threats.