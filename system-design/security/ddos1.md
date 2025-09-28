There are many different types of cyberattacks, but they can generally be grouped into categories based on their target and method. Distributed Denial of Service (DDoS) attacks are a specific type of attack focused on exhausting system resources.

Here's an overview of common attack types and how a Content Delivery Network (CDN) helps mitigate them.

***

## Types of Attacks

Attacks like DDoS are primarily classified into three main categories based on which layer of the network stack (using the OSI model) they target:

### 1. Distributed Denial of Service (DDoS) Attacks

DDoS attacks aim to make an online service unavailable by overwhelming it with a flood of traffic from multiple compromised computer systems (a **botnet**). This prevents legitimate users from accessing the service. DDoS attacks can be further broken down:

* **Volumetric Attacks (Layer 3/4):** These attacks use massive amounts of traffic to saturate the network bandwidth of the target. They are the most common type.
    * **UDP Flood:** Floods the target with User Datagram Protocol (UDP) packets, causing the server to expend resources sending "Destination Unreachable" messages.
    * **ICMP Flood (Ping Flood):** Overwhelms the target with Internet Control Message Protocol (ICMP) Echo Request packets (pings), consuming incoming and outgoing bandwidth.
    * **DNS Amplification:** Exploits misconfigured Domain Name System (DNS) servers to turn small queries into large responses directed at the victim, amplifying the traffic volume.
* **Protocol Attacks (Layer 3/4):** These attacks consume server resources, such as firewalls and load balancers, by exploiting weaknesses in network protocols.
    * **SYN Flood:** Exploits the Transmission Control Protocol (TCP) three-way handshake by sending a flood of initial connection requests (**SYN** packets) but never sending the final acknowledgment, leaving the server's ports open and exhausting its connection resources.
    * **Ping of Death:** Sends oversized Internet Protocol (IP) packets that, when reassembled, crash the target system due to memory buffer overflows.
* **Application-Layer Attacks (Layer 7):** These are the most sophisticated, as they target vulnerabilities in the specific application or server software. They are difficult to distinguish from legitimate high-volume traffic.
    * **HTTP Flood:** Mimics normal user behavior by repeatedly sending a high volume of HTTP GET or POST requests, aiming to exhaust the application's processing power.
    * **Slowloris:** Sends partial HTTP requests to a web server and keeps the connections open as long as possible by sending subsequent header data very slowly, eventually maxing out the server's available connection pool.

***

### 2. Web Application Attacks (Non-DDoS)

These attacks directly target vulnerabilities within the web application code or design, often to steal data or take control of the system, rather than just shut down the service.

* **SQL Injection (SQLi):** Inserts malicious SQL code into input fields (like login forms) to trick the application into executing unauthorized database commands, often to view, modify, or delete data.
* **Cross-Site Scripting (XSS):** Injects malicious client-side script (e.g., JavaScript) into web pages viewed by other users. This script can be used to hijack user sessions, steal cookies, or redirect users.
* **Broken Authentication/Access Control:** Exploits flaws in authentication (login/password) or authorization (what a user is allowed to do) to gain unauthorized access to accounts, resources, or administrative functions.
* **Security Misconfiguration:** Exploits security flaws caused by improper setup, such as leaving default credentials, exposing unneeded services, or having incorrect file permissions.

***

## How a CDN Prevents Attacks

A **Content Delivery Network (CDN)** is a geographically distributed network of proxy servers and their data centers. While its primary role is to cache content and accelerate website loading, its architecture makes it an extremely effective first line of defense against many types of cyberattacks, especially DDoS.

### DDoS Mitigation

A CDN's ability to absorb and filter malicious traffic is its strongest defense:

1.  **Traffic Absorption and Distribution (Defense against Volumetric Attacks):**
    * **Massive Capacity:** CDNs have a vast, distributed network capacity that can absorb the massive traffic volumes of even the largest DDoS attacks. Instead of the attack traffic hitting a single origin server, it's diffused across the entire CDN network, rendering the flood ineffective.
    * **Load Balancing:** Traffic is automatically distributed across numerous CDN edge servers globally. This prevents any single point from being overwhelmed, keeping the website available.

2.  **Traffic Filtering and Scrubbing (Defense against Protocol and Application Attacks):**
    * **Edge Processing:** Because the CDN sits between the internet and the origin server, it can analyze all incoming traffic at the **network edge**. It uses sophisticated security tools (sometimes integrated with an Anycast network) to inspect and filter malicious requests before they ever reach the origin server.
    * **Rate Limiting:** A CDN can implement rate limiting to restrict the number of requests allowed from a single IP address or session within a specific timeframe, effectively neutralizing HTTP Floods and slow-and-low attacks like Slowloris.
    * **Challenge-Response Tests:** The CDN can deploy tests like CAPTCHAs or JavaScript challenges to verify if a request is coming from a legitimate human browser or an automated botnet.
    * **IP Black/Whitelisting:** Known malicious IPs or entire geographic regions can be instantly blocked.

3.  **Hiding the Origin Server:**
    * The CDN acts as a **proxy**, obscuring the true IP address of the origin server. Since the attacker doesn't know the server's real address, they cannot directly target it, forcing them to attack the more robust CDN infrastructure instead.

### Web Application Attack Mitigation

Modern CDNs often integrate advanced security features to combat Layer 7 application attacks:

* **Web Application Firewall (WAF):** Many CDN services include a WAF, which operates at the application layer to inspect HTTP traffic. It uses rule sets to identify and block common application exploits like **SQL Injection** and **Cross-Site Scripting (XSS)** by validating inputs and blocking suspicious request patterns.
* **Bot Management:** Sophisticated bot management tools within the CDN can distinguish between malicious bot traffic and good bot traffic (like search engine crawlers), preventing attacks that rely on automation.
