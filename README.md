# Network Threat Detection & Audit Evidence Collection | Suricata + Wireshark

![Status](https://img.shields.io/badge/Status-Completed-28A745?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=linux&logoColor=white)
![Suricata](https://img.shields.io/badge/Suricata-8.0.3-orange?style=for-the-badge)
![Wireshark](https://img.shields.io/badge/Wireshark-Live%20Capture-1679A7?style=for-the-badge&logo=wireshark)
![Alerts](https://img.shields.io/badge/Alerts%20Triaged-250-red?style=for-the-badge)
![Framework](https://img.shields.io/badge/Framework-ISO%2027001%20%7C%20NIST%20CSF-0057A8?style=for-the-badge)
![Kill Chain](https://img.shields.io/badge/Mapped-Cyber%20Kill%20Chain-blueviolet?style=for-the-badge)

**A structured network threat detection exercise using Suricata IDS and Wireshark to monitor, triage, and document a multi-stage attack sequence.** 
This project validates a detective control (Suricata IDS) by simulating a real-world kill chain (Reconnaissance → Scanning → Initial Access). It produces audit-grade evidence using independent Wireshark packet captures to confirm true positives, honestly documents environmental testing limitations, and maps findings to ISO 27001 and NIST CSF.

---

## Project at a Glance

| Area | Details |
|:---|:---|
| **Threat Detected** | Network Reconnaissance (Ping Sweep), Port Scanning (Nmap SYN), and SSH Brute Force (T1046, T1110). |
| **Control Tested** | Suricata IDS 8.0.3 with 48,701 Emerging Threats rules + 5 custom rules. |
| **Lab Environment** | Suricata IDS (Kali) monitoring a Host-Only Network against a Windows attacker. |
| **Key Finding** | 250 Alerts generated. 4 out of 5 custom rules successfully validated with independent Wireshark packet evidence. |
| **True GRC Value** | **Honest Control Limitation Documented:** One HTTP rule did not fire because the target web service was down. This was documented transparently rather than misrepresented. |
| **Framework Mapped** | ISO 27001 (A.8.16, A.8.7), NIST CSF (DE.CM-1, DE.AE-2, RS.CO-2). |

---

## Repository Contents

Navigate this repository to view the formal GRC artifacts:

| Document | Description |
|:---|:---|
| **`docs/01-Executive-Summary.md`** | A non-technical summary for senior leadership validating the network detection control and highlighting the "honest limitation" approach. |
| **`docs/02-Executive-Dashboard.md`** | A one-page metrics dashboard for CISO and Board-level review. |
| **`docs/03-Scope-Methodology.md`** | The formal Terms of Reference, covering rule design, threshold risk decisions, and the live attack simulation. |
| **`docs/04-Assumed-Business-Context.md`** | The hypothetical threat model, business drivers, and risk appetite. |
| **`docs/05-Risk-Register.md`** | A formalized risk register entry for the Network Scanning/Brute-Force vulnerability. |
| **`docs/06-Remediation-Tracker.md`** | A project management artifact tracking SOAR integration and automated blocking, complete with Cost-Benefit Analysis. |
| **`docs/07-Compliance-Gap-Analysis.md`** | A control-by-control analysis mapping the IDS deployment to ISO 27001 Annex A, NIST CSF, and PCI DSS. |
| **`docs/08-Asset-Business-Criticality.md`** | Contextualizing the monitored Kali host as a critical internal application server. |
| **`docs/09-MITRE-ATTACK-Mapping.md`** | Detailed mapping of the attack sequence (Reconnaissance → Scanning → Brute Force) to MITRE ATT&CK. |
| **`docs/10-Residual-Risk-Assessment.md`** | A calculation of the remaining risk exposure after applying control recommendations. |
| **`docs/11-Lessons-Learned.md`** | A post-implementation retrospective capturing methodology improvements for future control tests. |
| **`artifacts/`** | Raw forensic evidence, including the custom `local.rules`, `fast.log` (250 alerts), `eve.json`, and the full 8,344-packet `soc3_capture.pcapng`. |
| **`screenshots/`** | 16 annotated screenshots documenting the IDS install, rule loading, and Wireshark packet filters. |

## Screenshots Index

| # | Filename | Description |
|---|---|---|
| 01 | GRC5-01-suricata-version-verified.png | Suricata 8.0.3 installation verified |
| 02 | GRC5-02-suricata-yaml-homenet-afpacket.png | HOME_NET and interface configuration |
| 03 | GRC5-03-suricata-rules-updated.png | Emerging Threats ruleset — 48,701 rules enabled |
| 04 | GRC5-04-suricata-config-test-passed.png | Configuration validation — 0 rules failed |
| 05 | GRC5-05-custom-rules-validated.png | 5 custom detection rules loaded and validated |
| 06 | GRC5-06-suricata-running-live.png | Suricata engine running live on eth0 |
| 07 | GRC5-07-wireshark-capturing-eth0.png | Wireshark independent capture started |
| 08 | GRC5-08-attack-traffic-generated.png | Controlled attack traffic generated from source host |
| 09 | GRC5-09-suricata-fast-log-alerts.png | fast.log — 250 total alerts summary |
| 10 | GRC5-10-suricata-eve-json-alerts.png | eve.json — structured SIEM-ingestible alert data |
| 11 | GRC5-11-wireshark-save-capture.png | Packet capture saved — grc5_capture.pcapng |
| 12 | GRC5-12-wireshark-filter-icmp-ping.png | ICMP reconnaissance — 34 packets, TTL=128 confirmed |
| 13 | GRC5-13-wireshark-filter-nmap-syn-scan.png | Nmap SYN scan — 3,130 packets, Win=1024 signature |
| 14 | GRC5-14-wireshark-filter-ssh-bruteforce.png | SSH brute force — 123 packets, incrementing source ports |
| 15 | GRC5-15-wireshark-protocol-hierarchy.png | Protocol hierarchy — TCP 75% anomaly indicator |
| 16 | GRC5-16-wireshark-conversations-tcp.png | TCP conversations — 3,130 incomplete handshake pattern |
