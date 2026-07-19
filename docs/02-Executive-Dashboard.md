# 2. Executive Dashboard — Network Detection Control Posture

**Assessment Date:** February 25, 2026  
**Dashboard Owner:** Niladri Biswas  
**Asset Scope:** Internal Network Segment (`192.168.56.0/24`) — HR/Payroll Application Server

---

## Risk Heatmap — Current Posture

| Risk Domain | Inherent Risk | Control Effectiveness | Residual Risk |
|:---|:---:|:---:|:---:|
| **Network Reconnaissance (ICMP Sweep)** | 🔴 High | ✅ High | 🟡 Medium |
| **Port Scanning (Nmap SYN)** | 🔴 High | ✅ High | 🟡 Medium |
| **Credential Access (SSH Brute Force)** | 🔴 High | ✅ High | 🟡 Medium |
| **Application-Layer HTTP Scanning** | 🟠 Medium | ⚠️ Unverified | 🟠 Medium |
| **DoS / Flood Patterns** | 🟡 Medium | ✅ High | 🟢 Low |

---

## Key Metrics — 24-Hour Period

| Metric | Value |
|:---|:---:|
| **Network Packets Captured** | 8,344 |
| **Total IDS Alerts Generated** | 249 |
| **Emerging Threats Rules Loaded** | 48,701 |
| **Custom Rules Deployed** | 5 |
| **Custom Rules Triggered & Validated** | 4 |
| **True Positive Rate (Triggered Rules)** | 100% ✅ |
| **Unverified Rules (Environmental Gap)** | 1 (SID 9000004) ⚠️ |

---

## Alert Triage Summary

| Attack Stage | MITRE ATT&CK | Rule SID | Alert Count | Validation Status |
|:---|:---|:---:|:---:|:---|
| **Reconnaissance** | T1595.001 | 9000001 | 4 | ✅ Confirmed True Positive (ICMP Echo) |
| **Port Scanning** | T1046 | 9000002 | 156 | ✅ Confirmed True Positive (SYN Flood) |
| **Credential Access** | T1110.001 | 9000003 | 24 | ✅ Confirmed True Positive (SSH Brute Force) |
| **DoS / Flooding** | T1498 | 9000005 | 30 | ✅ Confirmed True Positive (High SYN Rate) |
| **App-Layer Recon** | T1595.002 | 9000004 | 0 | ⚠️ **Unverified** — Environment gap (No web service) |
| **ET INFO (DHCP Hostname)** | T1046 | 2022973 | 35 | ℹ️ Informational — Background noise (DHCP fingerprinting) |
| **TOTAL** | | | **249** | |
---

## Control Status Dashboard

| Detective Control | Operational | Validated | Automatically Responding |
|:---|:---:|:---:|:---:|
| **Suricata IDS (48,701 ET Rules)** | ✅ Active | ✅ Passed | ❌ Not yet (Manual review only) |
| **Custom Ping Sweep Rule** | ✅ Active | ✅ Passed | ❌ Not yet |
| **Custom Nmap SYN Rule** | ✅ Active | ✅ Passed | ❌ Not yet |
| **Custom SSH Brute Force Rule** | ✅ Active | ✅ Passed | ❌ Not yet |
| **Custom HTTP Agent Rule** | ✅ Active | ⚠️ Unverified | ❌ Not yet |
| **Packet Capture Audit Trail** | ✅ Active | ✅ Passed | ✅ Yes (PCAP stored) |

---

## Immediate Executive Action Items

| Priority | Action | Owner | Est. Cost |
|:---:|:---|:---|:---:|
| 🔴 **P1** | Implement SOAR automation to block scanning IPs upon detecting SID 9000002 (5+ SYN packets). | SOC Lead | ~$300 |
| 🟠 **P2** | Deploy test web server to validate SID 9000004 (HTTP rule) and close the verification gap. | AppSec Engineer | ~$50 |
| 🟡 **P3** | Expand Suricata sensor placement to perimeter gateways to eliminate single-segment blind spots. | Network Architect | ~$300 |

---

> **Dashboard Conclusion:** The detective control framework is operationally sound with a 100% true-positive rate on the attack paths it was able to exercise. The primary immediate gap is the lack of *preventive* automation (SOAR) and an unverified rule due to environmental configuration. Both are low-cost, high-impact remediations.
