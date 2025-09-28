## ⚔️ Types of Cyberattacks

Cyberattacks can be broadly categorized into three groups based on their target: **Volumetric**, **Protocol**, and **Application-Layer**.

***

### 1️⃣ Distributed Denial of Service (DDoS) Attacks 🛡️

A DDoS attack's goal is to overwhelm a system to deny service to legitimate users. The attack is sourced from a **botnet** (a network of compromised devices).

| Attack Type | Symbol | Target / Goal | Examples |
| :--- | :--- | :--- | :--- |
| **Volumetric Attacks** | 🌊 | **Bandwidth.** To consume all network capacity with sheer traffic volume. | **UDP Flood**, **ICMP Flood**, **DNS Amplification** (small queries lead to massive responses). |
| **Protocol Attacks** | 🔗 | **Network Resources (Firewalls/Load Balancers).** To deplete server's connection state tables. | **SYN Flood** (incomplete connection requests), **Ping of Death** (oversized packet fragments). |
| **Application-Layer Attacks** | 💻 | **Application/Server Software (Layer 7).** To exhaust the application's resources with seemingly legitimate requests. | **HTTP Flood** (repeated GET/POST requests), **Slowloris** (slow, partial HTTP requests). |

***

### 2️⃣ Web Application Attacks 🔓

These attacks target vulnerabilities in the application code itself, often to steal data or gain unauthorized access. They are frequently monitored by the **OWASP Top 10** organization.

| Attack Type | Symbol | Target / Goal | Mitigation Concept |
| :--- | :--- | :--- | :--- |
| **SQL Injection (SQLi)** | 🗄️ | **Database.** Inserts malicious commands into input fields to manipulate the database. | **Input Sanitization** and **Parameterized Queries**. |
| **Cross-Site Scripting (XSS)** | 📜 | **User's Browser.** Injects malicious script to run in a victim's web session (e.g., to steal cookies). | **Output Encoding** (treating user input as data, not code). |
| **Broken Access Control** | 🔑 | **User Privileges.** Exploits flawed security to allow a user to access resources they shouldn't. | **Principle of Least Privilege** and strict authorization checks. |
| **Security Misconfiguration** | ⚙️ | **Server/Application Setup.** Exploits default settings, unpatched software, or exposed services. | **Security Hardening** and regular patch management. |

***

## 🌐 How a CDN Prevents Attacks

A **Content Delivery Network (CDN)** is a geographically distributed network of proxy servers that acts as a powerful shield, sitting between the attackers and your original servers (the **origin**).

| Defense Mechanism | Symbol | Function / Attack Type Mitigated |
| :--- | :--- | :--- |
| **Traffic Absorption & Scale** | 🧱 | **Mitigates Volumetric Attacks.** The CDN's massive, distributed global capacity absorbs and diffuses traffic floods, preventing any single point from being overwhelmed. |
| **Origin Shielding** | 👻 | **Mitigates Direct Assaults.** By acting as a **reverse proxy**, the CDN hides the true **IP address** of the origin server, preventing attackers from bypassing defenses. |
| **Web Application Firewall (WAF)** | 🚨 | **Mitigates Application Attacks (Layer 7).** Inspects incoming HTTP requests for malicious patterns and blocks exploits like **SQLi** and **XSS**. |
| **Rate Limiting & Bot Management** | 🤖 | **Mitigates HTTP/SYN Floods and Slowloris.** Controls the number of requests per second from a single source, distinguishing between legitimate users and malicious bots. |
| **Geo-Blocking & Filtering** | 🗺️ | **Mitigates Known Threats.** Filters traffic based on reputation, automatically blacklisting suspicious **IP addresses** and known attack sources. |
