

# 🔥 Firewall Behavior & Network Reconnaissance Lab (pfSense)

## 📌 Overview

This project demonstrates how firewall rules, static routing, and Network Address Translation (NAT) impact communication between an external attacker and a protected internal network.

Using a controlled lab environment, I started with **default secure configurations** in pfSense and intentionally weakened them step-by-step to observe how misconfigurations expose internal systems.

---

## 🎯 Objectives

* Understand default WAN/LAN firewall behavior
* Simulate external reconnaissance using Kali Linux
* Analyze the role of routing and NAT in communication
* Demonstrate how misconfigurations expose internal networks

---

## 🏗️ Lab Architecture

```text
[Kali Linux - External Attacker]
        |
   (Bridged Network - DHCP)
        |
[pfSense Firewall]
   WAN: 10.x.x.x
   LAN: 192.168.50.1/24
        |
[Internal Network]
   - Active Directory (192.168.50.99)
   - Windows 10 (192.168.50.11)
   - Ubuntu + Splunk (192.168.50.100)
   - Metasploitable (192.168.50.12)
```

<img width="1161" height="512" alt="Screenshot 2026-05-04 091116" src="https://github.com/user-attachments/assets/25d57c3b-0d70-43f3-965a-4418414eef70" />


## ⚠️ Note on IP Addressing

WAN-side IP addresses were dynamically assigned via DHCP and changed over time. Some screenshots may show different IPs, but all results and concepts are independent of specific IP values.

---

## 🧪 Methodology

### Phase 1: Default Secure State

* WAN blocked all inbound traffic
* LAN allowed outbound traffic

**Result:**

* Ping and Nmap scans from Kali failed ❌

👉 Insight: pfSense enforces a strong default-deny policy.

---

### Phase 2: Allowing WAN Access

* Enabled ICMP and TCP to pfSense WAN IP

**Result:**

* WAN interface became reachable from Kali✅

---

### Phase 3: Attempting LAN Access

* Tried accessing the internal gateway and hosts
* WAN → LAN traffic blocked

**Result:**

* No access to internal network ❌

---

### Phase 4: Routing + Firewall Rules

* Added broad WAN → LAN rule on pfSense.
* Added static route on Kali

```bash
sudo ip route add 192.168.50.0/24 via <pfSense-WAN-IP>
```

**Result:**

* Default Gateway accessible from kali✅
* Internal devices still unresponsive ❌

👉 Routing ≠ full communication

---

### Phase 5: Return Path Problem

* Traffic reached internal hosts
* No replies returned to kali

👉 One-way communication only

---

### Phase 6: NAT Implementation

* Configured NAT on pfSense to translate WAN traffic

**Result:**

* Bidirectional communication established ✅
* Internal hosts responded

---

### Phase 7: Network Reconnaissance

```bash
nmap -sS -T4 192.168.50.0/24
nmap -A 192.168.50.0/24
```

**Before NAT:**

* Hosts appeared down ❌

**After NAT:**

* Full host discovery ✅
* Open ports visible

---

## 🔍 Key Findings

* WAN blocks inbound traffic by default
* Routing enables access, not an established communication
* NAT is required for return traffic
* Nmap needs bidirectional/communication to work effectively
* Misconfigurations can quickly expose an entire internal network

---

## 🧠 Core Networking Model
Successful external-to-internal communication requires:

1. **Permission (Firewall Rules)**
2. **Reachability (Routing)**
3. **Return Path (NAT)**

---

## 🚨 Security Recommendations

* Avoid broad WAN → LAN rules
* Enforce least privilege access
* Restrict exposure to required ports only
* Enable logging and monitoring (e.g., Suricata + Splunk)

---

## 🏁 Conclusion

This lab demonstrates how improper firewall and NAT configurations can expose internal systems to external reconnaissance.

Understanding the relationship between firewall rules, routing, and NAT is critical for building secure network architectures.

---

## 💡 Skills Demonstrated

* Firewall configuration
* Routing and troubleshooting
* NAT implementation
* Network reconnaissance
* Security analysis

---
