# NETWORKWALKS-MOHDISSA-B084-WK2-PM1-PM5-FINAL

---

## 📋 Assessment Overview & Summary

| Parameter | Assessment Verification Details |
| :--- | :--- |
| **Pentester Name** | Mohdissa Fizza |
| **Program / Batch ID** | B084-Networkwalks |
| **Date** | 14 November 2026 |
| **Modules Completed** | W2-PM1 (Kali OSINT Tools) & W2-PM5 (Zenmap Scanning) |
| **Target Scope** | 1. External Target: `networkwalks.com`<br>2. Internal Subnet: `192.168.56.0/24` (Host-Only Virtual LAN) |
| **Authorization** | Formal Written Authorization Secured Prior to Testing |

---

## ⚖️ 1. Liability Disclaimer & Authorization
I have performed all reconnaissance, footprinting, and scanning activities strictly on authorized systems where formal written permission was secured in advance, or within private virtualized network segments that I directly own and control. This assessment report is compiled solely for educational, research, and defensive hardening purposes under the curriculum of the Networkwalks Cybersecurity Program.

Unauthorized access, non-consensual probing, port scanning, or attacking systems without explicit written consent is illegal under the Computer Fraud and Abuse Act (CFAA), the Computer Misuse Act 1990, and international cyber legislation. Every action taken during this assessment complied with the established Rules of Engagement (RoE). The author, the instructors, and Networkwalks assume no liability for the misuse of this technical information. Unlawful exploitation may result in criminal prosecution, severe fines, and professional disqualification.

---

## 🎯 2. Introduction
This technical assessment report documents the practical execution of Week 2 coursework within the Networkwalks Cybersecurity & Ethical Hacking Program, encompassing two interconnected phases of the Penetration Testing Execution Standard (PTES):
* **Module W2-PM1:** Passive Footprinting & Open Source Intelligence (OSINT) gathering against the target domain `networkwalks.com` using six specialized Kali Linux reconnaissance utilities.
* **Module W2-PM5:** Active Network Scanning and local host discovery across a Class C subnet (`192.168.56.0/24`) utilizing the Zenmap graphical interface and the Nmap scanning engine.

Together, these modules demonstrate the critical transition from passive, zero-touch public intelligence gathering into direct, active network mapping.

---

## 🛠️ 3. Tools Used

| Tool / Platform | Operating System | Operational Role & Purpose |
| :--- | :--- | :--- |
| **Kali Linux** (`mohdissa@kali`) | Linux (Rolling) | Primary reconnaissance operating system housing footprinting toolsets. |
| **Windows 10/11** | Windows | Host environment executing network interface scoping and Zenmap GUI scans. |
| **WHOIS** | Kali Linux | Queries registrar databases for domain ownership, registration dates, and nameservers. |
| **WhatWeb** | Kali Linux | Fingerprints web server software, CMS builds, plugins, frameworks, and IP headers. |
| **Nslookup** | Kali Linux | Performs DNS resolution to determine domain-to-IP mappings via DNS resolvers. |
| **cURL (`-I`)** | Kali Linux | Extracts HTTP/HTTPS response headers, caching layers, and REST API endpoints. |
| **Wafw00f** | Kali Linux | Fingerprints Web Application Firewalls (WAF) protecting the target application. |
| **DNSRecon** | Kali Linux | Advanced DNS enumeration analyzing SOA, NS, MX, A, TXT (SPF), and SRV records. |
| **Zenmap (Nmap 7.99)** | Windows | Performs ping sweeps (`-sn`) across subnets to discover live hosts and map topology. |
| **Windows ipconfig** | Windows | Identifies local network adapter IP, subnet mask, gateway, and MAC addresses. |

---

## 🔬 4. Activities Performed

### 4.1 Section 1: Footprinting Results (Module W2-PM1)
* **Tool 1: WHOIS (`whois networkwalks.com | head -n 30`)** — Identified GoDaddy registration, creation date (`2019-11-06`), expiration (`2027-11-06`), and HostGator nameservers (`NS6135.HOSTGATOR.COM`).
* **Tool 2: WhatWeb (`whatweb networkwalks.com | head -n 30`)** — Fingerprinted Apache web server, WordPress CMS `7.1.4`, WP Download Manager `3.3.76`, Bootstrap `7.1.4`, and public IP `192.232.216.135`.
* **Tool 3: Nslookup (`nslookup networkwalks.com | head -n 30`)** — Resolved canonical domain to public IPv4 address `192.232.216.135`.
* **Tool 4: cURL (`curl -I https://networkwalks.com`)** — Extracted HTTP 200 OK headers, security policies, and WordPress REST API endpoints (`/wp-json/`).
* **Tool 5: Wafw00f (`wafw00f networkwalks.com`)** — Positively identified **ModSecurity (SpiderLabs)** WAF protecting the target application.
* **Tool 6: DNSRecon (`dnsrecon -d networkwalks.com | head -n 30`)** — Enumerated SOA, BIND version `9.16.23-RH`, MX routes, and SPF policy (`~all`).

### 4.2 Section 2: Network Scanning Results (Module W2-PM5)
* **Interface Scoping (`ipconfig /all`)** — Identified VirtualBox Host-Only adapter (`Ethernet 2`) configured at IPv4 `192.168.56.1` with subnet mask `255.255.255.0` (`/24`).
* **Zenmap Ping Scan (`nmap -sn 192.168.56.0/24`)** — Scanned 256 addresses in 12.28 seconds, discovering live host `192.168.56.1` (`MAC: 0A-00-27-00-00-0B`).
* **Topology Visualization** — Mapped radial layout confirming direct Layer 2/3 adjacency between `localhost` and `192.168.56.1`.

---

## ⚠️ 5. Risk Analysis & Threat Modeling

| # | Finding / Observation | Evidence & Parameters | Threat Scenario & Impact | CVSS v3.1 | Risk Level |
| :-: | :--- | :--- | :--- | :---: | :---: |
| **1** | Web Technology Version Exposure | WordPress 7.1.4 & WP Download Manager 3.3.76 | Attackers search CVE/NVD databases for unpatched RCE or file upload vulnerabilities. | 5.3 | Medium |
| **2** | Direct Origin IP Exposure | Nslookup & DNSRecon resolved to `192.232.216.135` | Allows adversaries to bypass edge proxy DDoS protections and target origin web server directly. | 3.7 | Low |
| **3** | REST API Endpoint Exposure | cURL extracted Link header pointing to `/wp-json/` | Unauthenticated attackers query `/wp-json/wp/v2/users` to enumerate valid usernames. | 5.3 | Medium |
| **4** | WAF Technology Fingerprinted | Wafw00f identified ModSecurity (SpiderLabs) | Adversaries craft custom evasion payloads (comment splitting, chunking) to bypass OWASP CRS. | 3.1 | Low |
| **5** | BIND DNS Version Disclosed | DNSRecon queried BIND version `"9.16.23-RH"` | Exposes exact Red Hat package release, allowing version-specific denial-of-service exploits. | 5.3 | Medium |
| **6** | Permissive SPF Policy (`~all`) | DNSRecon extracted SPF `v=spf1 ... ~all` | SoftFail allows domain-spoofed emails to reach inbox folders rather than being rejected. | 4.3 | Medium |
| **7** | Subnet Host Discoverability | Zenmap discovered `192.168.56.1` on local subnet | Live host visibility provides entry points for lateral movement if rogue devices join segment. | 3.9 | Low |

*Note: These findings represent architectural observations and reconnaissance exposures. No exploitation was executed during these introductory modules.*

---

## 🛡️ 6. Defensive Hardening Recommendations
1. **Suppress Software & Server Banners:** Configure Apache (`ServerTokens Prod`, `ServerSignature Off`) and suppress WordPress generator meta tags.
2. **Restrict WordPress REST API Access:** Disable public unauthenticated access to `/wp-json/wp/v2/users` to prevent user harvesting.
3. **Harden Email Authentication:** Upgrade SPF policy from SoftFail (`~all`) to HardFail (`-all`) and deploy a strict DMARC policy (`p=reject`).
4. **Conceal Origin Web Servers:** Restrict web server firewall rules so only authorized reverse proxy IPs can establish connections on ports 80/443.
5. **Suppress DNS BIND Banners:** Configure version `"None Available";` in `named.conf` to eliminate nameserver version disclosure.
6. **Maintain ModSecurity Ruleset Tuning:** Regularly update the OWASP Core Rule Set (CRS) and monitor WAF logs for evasion attempts.
7. **Network Segmentation & Isolation:** Enforce strict port security and isolated VLANs/virtual adapters to restrict unauthorized device discovery.

---

## 📊 7. Conclusion: Passive Reconnaissance to Active Discovery Transition
The successful completion of modules W2-PM1 and W2-PM5 demonstrates the foundational transition from Passive Reconnaissance (Footprinting) to Active Network Discovery (Scanning) within a structured penetration test. Passive reconnaissance operates completely out-of-band under total stealth, while active network discovery initiates direct packet transmission to confirm host availability and network topology. In professional security assessments, passive intelligence directly guides and optimizes active discovery, allowing the pentester to minimize scan noise and focus scanning resources exclusively on authorized assets.

---

## 🖼️ 8. Formal Evidences Index
* **Figure 1:** WHOIS Domain Registration Reconnaissance (`mohdissa@kali`)
<img width="951" height="476" alt="image" src="https://github.com/user-attachments/assets/e8291659-977e-4c3a-8170-be7c1fcd5f09" />

* **Figure 2:** WhatWeb Technology Stack Fingerprinting (`mohdissa@kali`)
<img width="951" height="476" alt="image" src="https://github.com/user-attachments/assets/81e650d8-2f58-4eeb-9fa6-6339c95fd722" />

* **Figure 3:** Nslookup DNS Authoritative Resolution (`mohdissa@kali`)
<img width="951" height="394" alt="image" src="https://github.com/user-attachments/assets/ff244332-4fe7-4096-b196-813d4cd9bf7b" />

* **Figure 4:** cURL HTTP Header & REST API Inspection (`mohdissa@kali`)
<img width="951" height="459" alt="image" src="https://github.com/user-attachments/assets/0f296510-3ec2-4d26-bcc5-2f410ac02ada" />

* **Figure 5:** Wafw00f ModSecurity WAF Detection (`mohdissa@kali`)
<img width="951" height="476" alt="image" src="https://github.com/user-attachments/assets/c5545445-faeb-416c-a4b4-e72f328204b7" />

* **Figure 6:** DNSRecon Comprehensive DNS Enumeration (`mohdissa@kali`)
<img width="951" height="476" alt="image" src="https://github.com/user-attachments/assets/086d2ad3-38dc-4313-b5da-8449ea3b2b12" />

* **Figure 7:** Zenmap Ping Scan Host Discovery (Windows `192.168.56.0/24`)
<img width="951" height="459" alt="image" src="https://github.com/user-attachments/assets/8519022c-c746-4999-b033-298d79952b50" />

* **Figure 8:** Zenmap Saved Network Topology Graphic (`localhost` to `192.168.56.1`)
<img width="820" height="623" alt="image" src="https://github.com/user-attachments/assets/9067d604-9a83-4b25-ae7b-cc343a167290" />


---
*Report Author: Mohdissa Fizza | Batch: B084-Networkwalks*  
*Course: Cybersecurity & Ethical Hacking Program | Week 02 Final Submission*
