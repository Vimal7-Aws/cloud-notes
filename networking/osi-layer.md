
# 🌐 The 7 Layers of the OSI Model (Security-Focused Guide)

The **OSI (Open Systems Interconnection) Model** divides network communication into **seven abstract layers**, each with distinct responsibilities.

Data flows:

- ⬇ **Down** the stack on the sending device  
- ⬆ **Up** the stack on the receiving device  

Each layer processes or interprets information before passing it along.

Understanding the OSI model is critical for **network engineers, security teams, DevOps professionals, and architects**, as it provides a structured way to apply defenses across the network stack.

---

# 📚 OSI Layers Overview

| Layer | Name | Primary Responsibility | Example Protocols |
|-------|------|------------------------|-------------------|
| 7 | Application | User-facing network services | HTTP, HTTPS, FTP, DNS |
| 6 | Presentation | Formatting, encryption, compression | SSL/TLS |
| 5 | Session | Session management & authentication | RPC |
| 4 | Transport | End-to-end delivery & reliability | TCP, UDP |
| 3 | Network | Routing & logical addressing | IP |
| 2 | Data Link | Local network communication | Ethernet, ARP |
| 1 | Physical | Transmission of raw bits | Cables, signals |

---

# 🟣 Layer 7 — Application Layer

The **Application Layer** is where users interact with the network.

Examples:
- Web browsers
- Email clients
- APIs

### Responsibilities
- Provides network services to applications
- Handles high-level protocols
- Closest layer to the end user

### Common Protocols
- HTTP / HTTPS
- FTP
- DNS

### 🚨 Common Threats
- Phishing & credential theft
- SQL Injection
- Cross-Site Scripting (XSS)
- Application-layer DDoS attacks

---

# 🔵 Layer 6 — Presentation Layer

The **Presentation Layer** ensures data is readable between systems.

### Responsibilities
- Data formatting
- Encryption / Decryption
- Compression / Decompression
- Encoding translation

If one system uses a different encoding format, Layer 6 translates it.

### 🚨 Common Threats
- Malware injection
- Protocol exploitation
- Compression attacks
- Encryption attacks

---

# 🟢 Layer 5 — Session Layer

The **Session Layer** manages communication sessions.

### Responsibilities
- Session establishment
- Authentication
- Checkpointing
- Session termination
- Reconnection handling

### Example Protocol
- RPC (Remote Procedure Call)

### 🚨 Common Threats
- Session hijacking
- Adversary-in-the-Middle (AiTM) attacks
- Replay attacks

---

# 🟡 Layer 4 — Transport Layer

The **Transport Layer** ensures reliable end-to-end data delivery.

### Responsibilities
- Segmentation
- Reassembly
- Error recovery
- Flow control
- Port management

### Protocols
- TCP
- UDP

### 🚨 Common Threats
- Port scanning
- SYN flood attacks
- UDP flood attacks

---

# 🟠 Layer 3 — Network Layer

The **Network Layer** determines how data travels across networks.

### Responsibilities
- Routing
- Forwarding
- Logical addressing (IP)
- Path determination

### Security Tools Operating Here
- Firewalls
- Routers
- Intrusion Detection Systems (IDS)

### 🚨 Common Threats
- IP spoofing
- IP fragmentation attacks
- Routing attacks
- Volumetric DDoS

---

# 🟤 Layer 2 — Data Link Layer

The **Data Link Layer** enables communication within the same local network.

### Responsibilities
- Framing
- MAC addressing
- Error detection
- Local delivery

Most switches operate at Layer 2.

### 🚨 Common Threats
- MAC spoofing
- VLAN hopping
- ARP poisoning

---

# ⚫ Layer 1 — Physical Layer

The **Physical Layer** transmits raw binary data.

### Components
- Cables
- Connectors
- Network interface hardware
- Electrical / optical signals

This layer converts data into 1s and 0s.

### 🚨 Common Threats
- Unauthorized device connections
- Cable tapping
- Signal interception

---

# 🧠 OSI Mnemonic (Top → Bottom)




---

<img width="1280" height="752" alt="image" src="https://github.com/user-attachments/assets/22738a93-54fb-46a3-a695-f52b54f61d88" />
