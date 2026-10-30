# NETWORKWALKS-B082-WK1-PMx-WIRESHARK-TRAFFIC-ANALYSIS



<div align="center">

# 🔐 SOC Analyst Lab: Traffic Analysis & Reconnaissance Detection 

**Detecting Reconnaissance & Port Scanning via Wireshark**
</div>

<p align="center">
  <!-- Core Networking, Tools & Protocol Badges -->
  <img src="https://img.shields.io/badge/Wireshark-404040?style=flat-square&labelColor=0070C0&logo=wireshark&logoColor=white" alt="Wireshark" />
  <img src="https://img.shields.io/badge/DNS-404040?style=flat-square&labelColor=0070C0" alt="DNS" />
  <img src="https://img.shields.io/badge/TCP-404040?style=flat-square&labelColor=0070C0" alt="TCP" />
  <img src="https://img.shields.io/badge/TLS-404040?style=flat-square&labelColor=0070C0" alt="TLS" />
  <img src="https://img.shields.io/badge/PCAP-404040?style=flat-square&labelColor=0070C0" alt="PCAP" />
  <img src="https://img.shields.io/badge/Packet%20Forensics-404040?style=flat-square&labelColor=0070C0" alt="Packet Forensics" />
  <img src="https://img.shields.io/badge/Protocol%20Analysis-404040?style=flat-square&labelColor=0070C0" alt="Protocol Analysis" />
  <img src="https://img.shields.io/badge/Malware%20Traffic%20Analysis-404040?style=flat-square&labelColor=C00000" alt="Malware Traffic Analysis" />



 
## 📌 Project Overview
In a production enterprise network, early detection of malicious reconnaissance is critical to stopping a full-scale cyber attack before it starts. This project simulates a real-world Security Operations Center (SOC) scenario where unusual traffic was reported heading towards an internal corporate web server. Using deep packet inspection (DPI) techniques within Wireshark, this lab walks through the exact steps required to identify anomalous scanning behavior, map out the adversary's footprint, and verify server vulnerability exposures.

* **Live Lab Environment:** [NetworkWalks Wireshark Lab 3](https://networkwalks.com)
* **Core Technical Competencies:** Network Traffic Analysis, Custom Display Filters, Protocol Verification (TCP Handshake), and Incident Attribution.

---

## 🎯 Lab Objectives
* **Isolate Connection Attempts:** Differentiate baseline network chatter from aggressive TCP handshake manipulation.
* **Trace Adversary Fingerprints:** Locate and isolate the attacker's unique Source IP, Target IP, and tracking port configurations.
* **Enumerate Attack Surface:** Analyze server responses (`SYN-ACK` vs `RST-ACK`) to definitively map out which corporate services are exposed (open) versus secured (closed).
* **Classify Threat Vectors:** Contextualize the raw packet metrics into an actionable security classification to assist in incident response reporting.

---

## 💡 Purpose of the Lab
The ultimate purpose of this exercise is to master the **Reconnaissance Phase of the Cyber Kill Chain**. Attackers rarely attack blind; they map network perimeters using "half-open" stealth scans to find open doors without triggering standard application logs. By completing this lab, a defender learns exactly how these stealth probes manipulate TCP flags at the packet layer and how to leverage specific display filters to quickly detect, quantify, and report unauthorized network discovery attempts.

---

## 🗺️ Network Topology



<img width="689" height="292" alt="real topogy diagram" src="https://github.com/user-attachments/assets/d95cce36-de34-4f8a-ba7d-b425cc5c88f4" />


---

## 🛠️ Investigation Walkthrough & Tasks

### Task 1: Baseline Capture Analysis
* **Objective:** Verify total packet ingestion metrics.
* **Analysis:** The total baseline packet capture ingests an absolute total of 309 packets.

### Task 2: Connection Inception Isolation (SYN Analysis)
* **Objective:** Filter traffic to reveal TCP connection initiations.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.syn == 1
  ```
* **Analysis:** Type `tcp.flags.syn == 1` in the filter bar and press Enter. The status bar shows exactly `Displayed: 73` packets matching the connection initiation criteria.

### Task 3: Attacker Tracking
* **Objective:** Determine the source machine committing systematic connection queries.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.syn == 1
  ```
* **Analysis:** Type `tcp.flags.syn == 1` in the filter bar and scroll through the Source column. One source, `192.168.10.66`, sends an automated block of about 40 packets from 3.4 seconds onward. That is the attacker.

### Task 4: Target Attribution
* **Objective:** Identify the targeted destination infrastructure.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.syn == 1
  ```
* **Analysis:** Type `tcp.flags.syn == 1` in the filter bar. The long block of packets comes from the attacker. The Destination column of those packets shows `192.168.10.20`, confirming the target is the web server.

### Task 5: Profiling the Adversary Vector - Source Footprint
* **Objective:** Identify the source ephemeral footprint used by the attacker.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.syn == 1
  ```
* **Analysis:** Type `tcp.flags.syn == 1` in the filter bar and look at the attacker's packets. In the Info column, the number before the arrow is the source port. It is `52311` in every packet, while the destination port changes each time.

### Task 6: Profiling the Adversary Vector - Probe Scope
* **Objective:** Trace the scope and breadth of the active probe.
* **Analysis:** Navigating to Statistics › Conversations › TCP Tab reveals that this single host rapidly scanned exactly 40 unique destination ports on the server.

### Task 7: Server Footprint - Active Services
* **Objective:** Identify which destination services are actively running on the target.
* **Wireshark Display Filter:** 
  ```text
  ip.addr == 192.168.10.66 && tcp.flags.syn == 1 && tcp.flags.ack == 1
  ```
* **Findings:** Ports 22 (SSH), 80 (HTTP), and 443 (HTTPS) are open, active, and listening (replied with `[SYN, ACK]`).

### Task 8: Server Footprint - Rejected Connections
* **Objective:** Determine which destination services are closed or rejected.
* **Wireshark Display Filter:** 
  ```text
  ip.addr == 192.168.10.66 && tcp.flags.reset == 1 && tcp.flags.ack == 1
  ```
* **Findings:** The target server's network stack successfully sent Reset responses back to the attacker on the remaining 37 closed ports.

### Task 9: Closed Ports Calculation
* **Objective:** Calculate the precise volume of non-responsive or closed services targeted.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.reset == 1 && tcp.flags.ack == 1 && ip.src == 192.168.10.20
  ```
* **Analysis:** Cross-referencing total connections shows 40 unique destination ports were actively targeted. Because 3 entry points are confirmed open, applying this filter isolates the responses from closed services, mathematically verifying that exactly 37 remaining ports are closed on the server.

### Task 10: Attack Mapping
* **Objective:** Correlate findings into a comprehensive attack footprint map.
* **Analysis:** The activity maps out as a standard TCP SYN port scan (stealth scan) targeted at 40 ports, discovering 3 open entry points and encountering 37 closed doors.

---

## 🛡️ Key Defensive Takeaways
1. **Stealth Scanning Mechanics:** Half-open scans let attackers determine port status via immediate kernel-level responses (`SYN-ACK` or `RST-ACK`) without committing to a full 3-way handshake, avoiding application-layer logging tools.
2. **Defensive Rulemaking:** Network security controls (Firewalls/IDS) should be configured with thresholds to detect an anomalous volume of `RST, ACK` rejections coming from single destination assets inside a localized time-window to trigger automated perimeter IP blocks.


##   🔐 Security & Ethical Use
This laboratory is intended strictly for education purposes only.

## 🔗 Tools & Resources

Wireshark Official Site: https://wireshark.org

Sample PCAP Repository: https://wireshark.org

PacketTotal Sandbox: https://packettotal.com

## 👤 Author

Anderson Edaiyo Uzezi

Cybersecurity Professional B082

LinkedIn: https://www.linkedin.com/in/anderson-edaiyo-bab74117b

## 📊 Project Information Matrix
> [!NOTE]
> This repository contains the complete documentation, environment blueprints, and live verification logs for my Week 01 specialized laboratory deployment focusing on deep packet inspection and network forensics.

🗂️ Project Parameter | ⚙️ Live Deployment Data
--- | ---
**Program Track** | Cybersecurity with Ethical Hacking
**Academy Module** | Networkwalks Academy Portfolio Matrix
**Timeline Horizon** | Week 01 Engineering Core
**Project Identity** | Wireshark Traffic Analysis
**Target Task ID** | TASK WK1-PM1
**Code Repository** | Public Deployment Hub via GitHub
