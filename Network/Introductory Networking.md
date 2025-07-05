
# Introductory Networking — TryHackMe

A beginner-friendly room covering key networking fundamentals: OSI & TCP/IP models, encapsulation, and essential tools.

---

## 🧩 Room Overview

Covers:

- The **OSI** and **TCP/IP** models
- **Encapsulation** & **De-encapsulation**
- Hands-on with tools: `ping`, `traceroute`, `whois`, `dig`

---

## 1. OSI Model (7 Layers)

1. **Physical** – hardware signals  
2. **Data Link** – MAC addressing, error checking  
3. **Network** – IP addressing, routing  
4. **Transport** – TCP/UDP, segments/datagrams  
5. **Session** – connection management  
6. **Presentation** – translation, encryption/compression  
7. **Application** – interface for apps

---

## 2. Encapsulation Process

Data moves down layers, gaining headers/trailers:

- Layer 7→5: “data”  
- Layer 4: **TCP segments** or **UDP datagrams**  
- Layer 3: **Packets**  
- Layer 2: **Frames**  
- Layer 1: **Bits**  

Reversed on receiving end.

---

## 3. TCP/IP Model

Simplified 4-layer model:

- **Application** (OSI 5–7)  
- **Transport** (OSI 4)  
- **Internet** (OSI 3)  
- **Network Interface** (OSI 1–2)

Explains the classic TCP three‑way handshake (SYN → SYN/ACK → ACK)

---

## 4. Networking Tools

### 🔹 Ping (ICMP)
Check host reachability, latency:  
```bash
ping <target>
```

Options:

- `-i`: interval between packets  
- `-4`: force IPv4  
- `-v`: verbose

---

### 🔹 Traceroute
Trace packet path across the network:  
```bash
traceroute <destination>
```
Options:

- `-i <iface>`: specify interface  
- `-T`: use TCP SYN

---

### 🔹 WHOIS
Get domain registration details:  
```bash
whois <domain>
```
Shows registrar, dates, contact info (e.g., postal code, tech email)

---

### 🔹 Dig (DNS lookup)
Query DNS records:  
```bash
dig <domain> [@server]
```
Shows IPs, TTL values in seconds (e.g., 86400 = 24h)

---

## 5. Answers to Common Questions

- Layer choosing TCP/UDP? → **4 (Transport)**  
- Layer checking packet corruption? → **2 (Data Link)**  
- Layer formatting data? → **2**  
- Layer transmitting data? → **1 (Physical)**  
- Layer encrypting/compressing? → **6 (Presentation)**  
- Layer maintaining sessions? → **5 (Session)**  
- Logical addressing? → **3 (Network)**  
- TCP “bite-sized” pieces? → **Segments** (UDP: **Datagrams**)

---

## ✅ Summary & Tips

You’ll learn:

- Core theoretical models (OSI & TCP/IP)  
- How data travels, encapsulates, and decapsulates  
- Essential diagnostic commands: `ping`, `traceroute`, `whois`, `dig`

A perfect stepping stone for networking and pentesting — **strongly recommended for beginners!**
