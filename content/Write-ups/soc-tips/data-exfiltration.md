---
title: "Spotting Data Exfiltration"
date: 2022-10-21T00:04:34+02:00
draft: true
tags: ["SOC", "Data Exfiltration"]
author: "@1nk0x01"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "How to Detect Data Exfiltration."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "https://miro.medium.com/max/640/1*Vcbfovigm3SEdluqzHkBog.png" # image path/url
    alt: "Data Exfiltration" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---


Hey folks, I am Mohamed Adel and I am a SOC Analyst 
Today we will talk about how to Spot Data Exfiltration

Let's Dive in!!

---------

First of all we should know what is data exfiltration. according to [MITRE ATT&CK®](https://attack.mitre.org/) Data Exfiltration is the Step when The adversary is trying to steal data may use to steal data from your network.

This could be done over Web Service, over USB, over Cloud Storage, Emails and many more, you can read more about it [Resource](https://attack.mitre.org/tactics/TA0010/)

First i left you some samples of how the exfiltration done with some of Exfiltration Methods not all of course but i will leave you more sources to dig deep into the topic.

### Exfiltrate via HTTP


![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-3-PowerShell-code-for-HTTP-POST-from-victim-host-plain.jpg)


### Exfiltrate via SMTP

SMTP is one of the most common methods for data exfiltration. Several malware programs exfiltrate the stolen information to an attacker-controlled SMTP server. For example [Agent Tesla](https://blogs.blackberry.com/en/2020/04/threat-spotlight-secret-agent-tesla) is a Windows-based keylogger and Remote Access Trojan (RAT) commonly uses SMTP to exfiltrate stolen data.

Attackers can use the following PowerShell code to send an email with an attached file (stolen data) to exfiltrate a remote address:


![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-8-Victim-host-send-stolen-data-to-attacker-control-email-box-using-SMTP--1024x80.jpg)


![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-9-Exfiltrate-data-delivered-to-attacker-email-box-1024x361.jpg)

**Stream**
![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-10-SMTP-streams--1024x468.jpg)

### Exfiltrate via DNS

> Tool used in demo: [dnsteal](https://github.com/m57/dnsteal)

![enter image description here](https://camo.githubusercontent.com/49b0419254d83b2abb9a4a9871eb55422a1ce10a034c132467144f344a36ab77/687474703a2f2f692e696d6775722e636f6d2f6e4a736f414d762e706e67)

![](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-12-victim-system-sends-targetdata.txt-over-DNS-1024x227.jpg)

![](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-13-Attacker-name-server-receive-target-data-using-DNS-is-a-communication-protocol--1024x514.jpg)


### Exfiltrate via IPv6
 ![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-22-IPv6teal-sender-script-running-on-victim-host--1024x90.jpg)
 shows the ‘IPv6teal receiver script’ running on the attacker-controlled machine; it is trying to listen and capture the targeted data:
 ![enter image description here](https://blog.apnic.net/wp-content/uploads/2022/03/Figure-23-IPv6teal-receiver-script-and-related-streams--768x275.jpg)

### You Will Find More Examples: [exfiltration-attacks](https://blog.apnic.net/2022/03/31/how-to-detect-and-prevent-common-data-exfiltration-attacks/)

## Detecting Data Exfiltration

so we are talking about transferring data so first thing is to check the volume of the data that being transfer over your network


- Monitor **High-Volume DNS tunneling** or **High-Volume Traffic being from unusual source but it is not should be 100% with high-volume.**

- a connection to unknown destination (C2) maybe

- Also, you have to check Process Creation Logs, maybe they compressed archive especially from CLI with password or 7z, WinRAR

- Pay attention to [**DLP**](), **UEBA alerts**
	the following file types to bypass DLP so take care:
	-   Plain Zip
	-   Password protected (AES) Zip
	-   Deeply nested Zips (many systems will stop scanning after 10-100 to avoid Zip Bombs)
	-   7zip
	-   RAR
	-   CAB
	-   Tar (+/- gzip)
	-   WIM image


- check multiple port firewall denies outbound from a single source

- Complex URLs or Unexplained URLs with long parameters
```
http://ooo.nu6tgnzvgm2tmmbzgq4a.rkgo---redacted---tw5.5z5i6fjnugmxfowy.beevish.com/xml.php?c=0&t=x&k=.txt&type=0&s=3500&n=example.txt
```

- They use Complex URLs to exfiltrate they data just to make you a bit blind or confused about the URL you will think a safe URL with params just like Microsoft URLs it has a long parameters but in fact they maybe carrying out some data

- **Monitoring for outbound traffic patterns**:- as malware needs to regularly communicate with C2 servers to maintain a consistent connection. Continuous monitoring provides opportunities to detect data exfiltration with common protocols such as HTTP:80 or HTTPS:443. It’s worth keeping in mind that some advanced malware randomize delays between C2 communications.



- **Keeping an up-to-date log of all approved IP addresses connections to compare against all new connections:** Along with this, it’s advised to keep an eye out for large data flows to unexpected IP addresses and major spikes in anomalous outbound traffic.

-    **Block unauthorized communication channels**: First, disable all unauthorized communication channels, ports and protocols by default, and re-enable on an as-needed basis.
- **Data Loss Prevention (DLP)**: Data loss prevention (DLP) is a strategy for detecting and preventing data exfiltration or data destruction. Many DLP solutions analyze network traffic and internal "[endpoint](https://www.cloudflare.com/learning/security/glossary/what-is-endpoint/)" devices to identify the leakage or loss of confidential information. Organizations use DLP to protect their confidential business information and [personally identifiable information (PII)](https://www.cloudflare.com/learning/privacy/what-is-pii/), which helps them stay compliant with industry and [data privacy](https://www.cloudflare.com/learning/privacy/what-is-data-privacy/) regulations.
---

Resources I used for this Article:

1. [attack.mitre.org](https://attack.mitre.org/)
2. [blog.apnic.net](https://blog.apnic.net/)
3. [www.sans.org](https://www.sans.org/)

-- Thanks For Reading.

