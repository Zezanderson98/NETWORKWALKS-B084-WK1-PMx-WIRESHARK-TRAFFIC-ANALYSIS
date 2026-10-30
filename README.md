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



<img width="688" height="320" alt="Topology diagram" src="https://github.com/user-attachments/assets/301fbb67-77b6-4bfe-b366-7e30c89f7326" />

---

## 🛠️ Investigation Walkthrough & Tasks

### Task 1: Baseline Capture Analysis
* **Objective:** Verify total packet ingestion metrics.
* **Analysis:** The total baseline packet capture ingests an absolute total of **309 packets**.

### Task 2: Connection Inception Isolation (SYN Analysis)
* **Objective:** Filter traffic to reveal TCP connection initiations.
* **Wireshark Display Filter:** 
  ```text
  tcp.flags.syn == 1
  ```
* **Metrics:** Isolating this parameter drops the packet list view to exactly **73 packets** matching the flag criteria.

### Task 3 & 4: Attacker Tracking & Target Attribution
* **Objective:** Determine the source machine committing systematic connection queries.
* **Analysis:** Evaluating the timeline past the `3.4` second mark reveals a rapid, automated burst of TCP connection requests originating directly from host **`192.168.10.66`** aimed squarely at the Web Server located at **`192.168.10.20`**.

### Task 5 & 6: Profiling the Adversary Vector
* **Objective:** Identify the source ephemeral footprint and trace the scope of the probe.
* **Analysis:** 
  * Inspection of the *Info* column blocks shows that the attacker is anchoring the entire scanning routine from a single static source port: **`52311`**.
  * Navigating to `Statistics › Conversations › TCP Tab` reveals that this single host rapidly scanned exactly **40 unique destination ports** on the server.

### Task 7, 8 & 9: Server Footprint & Attack Mapping 
* **Objective:** Determine which destination services are actively running vs rejected.
* **Analysis Filter:** 
  ```text
  ip.addr == 192.168.10.66
  ```
* **Findings:**
  * **Open Ports (Replied with `[SYN, ACK]`):** Ports **`22`** (SSH), **`80`** (HTTP), and **`443`** (HTTPS) are active and listening.
  * **Closed Ports (Replied with `[RST, ACK]`):** The target server's network stack successfully sent Reset responses back to the attacker on the remaining **37 closed ports**.

---

## 🎯 Final Security Threat Assessment
* **Identified Attacker IP:** `192.168.10.66`
* **Target Vector Host:** `192.168.10.20`
* **Attack Method Classification:** **TCP SYN Port Scan**
* **Impact Summary:** The adversary successfully enumerated three operational pathways (`22, 80, 443`) during this discovery sweep. This metadata could expose those services to tailored exploit attempts or brute-force tracking if perimeter blocking rules are not immediately enacted.

---

## 🛡️ Key Defensive Takeaways
1. **Stealth Scanning Mechanics:** Half-open scans let attackers determine port status via immediate kernel-level responses (`SYN-ACK` or `RST-ACK`) without committing to a full 3-way handshake, avoiding application-layer logging tools.
2. **Defensive Rulemaking:** Network security controls (Firewalls/IDS) should be configured with thresholds to detect an anomalous volume of `RST, ACK` rejections coming from single destination assets inside a localized time-window to trigger automated perimeter IP blocks.


  <!-- Profile & Platform Badges -->
  <img src="https://img.shields.io/badge/GitHub-404040?style=flat-square&labelColor=0070C0&logo=github&logoColor=white" alt="GitHub" />
  <img src="https://img.shields.io/badge/NetworkWalks-404040?style=flat-square&labelColor=C00000" alt="NetworkWalks" />
  <img src="https://img.shields.io/badge/Anderson%20Edaiyo-C00000?style=flat-square" alt="Anderson Edaiyo" />
</p>

