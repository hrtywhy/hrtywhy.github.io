---
layout:	post
title:	"Poisoning the Algorithm: How SEO Scams Exploit Google Gemini AI in Indonesia"
date:	2025-09-20
tags: Threat-Research
image:  '/images/medium/eye.gif'
imagehero: true
---

# Introduction

Search has always been a battleground between defenders and attackers. For decades, threat actors have abused search engine optimization (SEO) to manipulate what users see in the top results. Now, with the arrival of **AI-powered search assistants like Google Gemini AI Overview**, the stakes have never been higher.

I recently uncovered a campaign in Indonesia where attackers hijacked AI search results using **Black Hat SEO poisoning**. By flooding community websites with posts containing fraudulent customer service numbers, scammers trick users into calling fake helplines. Once connected, victims unknowingly hand over sensitive personal or financial information.

What makes this campaign unique is its integration with AI search: poisoned results not only appear on Google’s traditional search, but also in Gemini’s AI-generated answers — where users are more likely to *trust* the content.

# Key Takeaways

- **SEO poisoning is evolving**: Black Hat SEO is being weaponized to target trending **AI-related keywords**.
- **Community spam** is the delivery mechanism: attackers plant fraudulent content in **forums and knowledge-sharing sites**.
- **Scam phone numbers** are the payload: instead of malware, the goal is to redirect victims into **social engineering traps**.
- **AI amplifies poisoned content**: Google Gemini AI Overview can summarize or highlight malicious posts, giving them legitimacy.
- **This mirrors malware campaigns** like the recent [Badiis SEO poisoning campaign](https://unit42.paloaltonetworks.com/operation-rewrite-seo-poisoning-campaign/) revealed by Unit42, which used the same tactic to distribute malicious installers instead of scam numbers.

# Technical Analysis
### Poisoning Search Rankings
Attackers begin by identifying high-volume, high-intent queries often financial loan and service-related. Examples include:

- Cara Membatalkan Pinjaman (*how to cancel a loan*)
- Spinjam
- Batalkan Pinjaman

To hijack rankings, they deploy **Black Hat SEO tactics**:

1. **Keyword Stuffing** 
<br> Injecting target keywords unnaturally across forum posts, replies, and page metadata.
2. **Forum Spam**
<br> Mass-posting across platforms such as **Frammer Community, Google Support, Spotify Community, and Forum Bardi**.
3. **Link Farming** 
<br> Cross-linking posts from different spam accounts to inflate credibility.
4. **Content Cloaking** 
<br> Using benign text for crawlers but inserting scam numbers for human readers.

This ensures poisoned posts consistently rank in the **top 2–4 Google results**, where user clicks are concentrated.

![image.png](/images/tis.jpg)

### Luring Victims via Community Posts

Once indexed, these poisoned results resemble legitimate community Q&A threads. A victim searching for loan cancellation instructions might land on a forum post where multiple accounts are “discussing” the issue.

- **Fake engagement** 
<br> Dozens of replies from sockpuppet accounts reinforce credibility.
- **Injected contact numbers** 
<br> Each thread prominently displays “customer service” phone numbers — which belong to scammers.
- **Wide platform abuse** 
<br> The campaign spans multiple loan providers, including **Adakami, BTN, EasyCash, Kredit Cepat, Uangme, Lazbon and Gopay**.

The **illusion of consensus** (many users repeating the same number) dramatically increases trust.

![image.png](/images/ai-overview.png)


### The Social Engineering Payload

Unlike malware campaigns, this scam relies on **voice-based social engineering**:

1. Victim dials the fraudulent number.
2. A threat actor posing as a loan provider representative answers.
3. Victim is asked for loan IDs, KTP (Indonesian ID), or payment credentials.
4. Data is monetized directly (via fraud).

This pivot away from malware distribution reflects a low-cost, high-trust attack surface: no need to build payloads only manipulate SEO and deploy social engineering.


### Sentiment Analysis

Analysis of the Frammer Community was carried out by scraping data from the past 14 days, starting from when this post appeared:
- **Keyword frequency analysis** 
<br> Revealed terms tied to financial distress queries (pinjaman, spinjam, shopee, membatalkan).

![image.png](/images/word-cloud.png)

- **Account activity mapping**
<br> Identified a small set of actors (e.g., *“Gaja*,” *“Jamur*,” *“Rem”* and *“Widia”*) responsible for dozens of posts.


| **User Action** | **Sum Post** |
|---|---|
| Gaja posted | 21 |
| Jamur posted| 18 |
| Jamur replied| 16 |
| Widia replied| 13 |
| Rem posted | 13 |

- **Phone number clustering**
 <br> Highlighted repeat use of numbers such as *082118916821*, *082246435341*, and *+6283830504214*. The data was scraped using specific Indonesian-language scam-related keywords, then cleaned and processed with regex to achieve higher accuracy. This step was necessary because many contact numbers in the posts contained special characters (such as *+, -, ", or _*), so normalization was applied to ensure consistency in the phone number dataset.

| **User Action** | **Sum Post** |
|---|---|
| 082118916821 | 12 |
| 082246435341 | 11 |
| 085355042604 | 9 |
| 085919153702| 7 |
| 08154034985 | 4 |
| 08999909621 | 3
| 082175221891 | 3
| 0818834511 | 3
| 0811333256 | 1
| 083110610827 | 1
| 083830504214 | 1


This suggests automation pipelines for scraping trending queries, generating spam posts, and inserting phone numbers en masse.
![image.png](/images/chart.png)

### Comparative Case: Badiis Malware SEO Poisoning

The technique is not unique to scams. A recent campaign uncovered by Unit42 described attackers using SEO poisoning to distribute Badiis malware.

- **Same technique:** Black Hat SEO + poisoned results.
- **Different objective:** Instead of scam numbers, victims were redirected to sites hosting **malicious installers**.
- **Implication:** SEO poisoning is a *flexible delivery mechanism* that can be adapted for scams, malware, or phishing.

This highlights a convergence point: AI search systems are becoming a *new battleground* for both financially motivated scammers and advanced malware operators.

# Why Indonesia? Attribution and Motives

Indonesia has become a hotbed for SEO-poisoning scams due to several structural factors.

![image.png](/images/get-contact.jpg)

To verify the authenticity and patterns of the scam numbers observed, we cross-referenced them against datasets where users label suspicious or fraudulent contacts. The validation confirmed that multiple numbers carried fraud-related tags such as *“penipu”* (scammer), *“tipu”* (fraud), or “*customer service*” impersonation. This suggests the numbers are not random but part of a coordinated campaign leveraging social engineering.


![image.png](/images/tagging.jpg)

A closer look at tag distribution points to two distinct operational models:

- **Impersonation of customer support** 
<br> Attackers pose as official representatives of lending platforms, deceiving victims into sharing sensitive information.

- **Generic scam operations** 
<br> Numbers associated with aliases, scam warnings, or derogatory labels (e.g., “*Coky Habeahan*” or *“Wandi”*), indicating the involvement of a broader scammer ecosystem rather than a single actor.

This overlap shows how attackers recycle infrastructure, reusing the same phone numbers across campaigns while hiding behind multiple identities.

### 1. Unregulated Fintech Boom
Online lending apps such as Adakami, Adapundi, Uangme, EasyCash, and others dominate Indonesia’s financial ecosystem. Many of these operate in a gray regulatory zone, creating fertile ground for impersonation and fraud.

### 2. AI-Powered Search Adoption
With platforms like Google Gemini AI Overview being rolled out, attackers exploit growing trust in AI search outputs, poisoning them with maliciously-optimized results. Victims are directed toward fake “community” pages embedding scam numbers.

### 3. Digital Literacy Gaps
A significant portion of Indonesian users perceive search results and especially AI-generated summaries as authoritative. This reduced skepticism increases their vulnerability to scam numbers masquerading as official hotlines.

### 4. Low Barrier of Entry for Threat Actors
Unlike malware campaigns, SEO poisoning and social engineering require minimal technical expertise. Local scammers or loosely organized groups can scale cheaply, reusing poisoned infrastructure for mass fraud.

### 5. Possible Actor Profiles
Based on infrastructure overlap and tag analysis, these operations appear to be run by financially motivated local actors rather than sophisticated nation-state groups. Their emphasis is on volume-driven fraud rather than targeted espionage.


# Recommendations

### For Users
- **Verify contact numbers** only through official apps or websites.
- **Cross-check AI answers** with direct provider sources.
- **Be suspicious** of community forum numbers and “too helpful” posts.

### For Platforms
- **Deploy SEO poisoning detection:** Identify suspicious keyword stuffing and repeated phone numbers in forums.
- **Strengthen moderation:** Community platforms must invest in anti-spam systems that disrupt forum-based poisoning.

### For Enterprises
- **Brand monitoring:** Track misuse of company names in community spam.
- **Search visibility audits:** Ensure official numbers and support channels dominate search results.
- **Threat intelligence sharing:** Collaborate across sectors to expose poisoned domains and scam numbers.

# Conclusion

SEO poisoning has existed for years, but the rise of AI-powered search changes the threat landscape. What was once a nuisance of shady websites has evolved into AI-amplified social engineering pipelines. Indonesian campaign shows how scammers weaponize community forums, fake phone numbers, and Black Hat SEO** to prey on users. 

The takeaway is clear: **trust in AI search must be earned, not assumed**. Without aggressive detection and smarter safeguards, Gemini and other AI search tools risk becoming the next frontier for cybercriminals.