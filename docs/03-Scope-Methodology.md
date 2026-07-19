# 3. Assessment Scope & Investigation Methodology

**Document Type:** Terms of Reference (ToR)
**Assessment Period:** February 25, 2026
**Assessment Owner:** Niladri Biswas

## 3.1 Purpose
To formally deploy, configure, and validate a network threat detection control (Suricata IDS). The objective is to verify that custom and community rules accurately detect a simulated multi-stage cyber attack, map the alerts to packet-level evidence (Wireshark), and honestly document any environmental limitations for audit purposes.

## 3.2 In-Scope & Out-of-Scope Assets
| **Scope Element** | **Details** |
|:---|:---|
| **Detection Control** | Suricata 8.0.3 IDS running on a Kali Linux VM (`192.168.56.101`). |
| **Monitored Interface** | `eth0` (VirtualBox Host-Only Adapter). |
| **Attack Source** | Windows 10 Host (`192.168.56.107`) simulating the threat actor. |
| **Ruleset** | Emerging Threats (48,701 rules) + 5 custom `local.rules`. |
| **Out-of-Scope** | Exfiltration, C2 beaconing, and application-layer HTTP exploitation (due to lack of target service). |

## 3.3 Technical Testing Methodology (6 Phases)

**Phase 1 — IDS Deployment & Configuration**
Suricata 8.0.3 was installed on Kali. `HOME_NET` was correctly scoped to `192.168.56.0/24` to prevent false positives. The Emerging Threats ruleset was downloaded (64,554 total, 48,701 enabled).

**Phase 2 — Custom Rule Design & Loading**
Five custom rules were written to detect specific attack patterns:
*   **SID 9000001:** ICMP Ping Sweep (5 pings in 10 seconds).
*   **SID 9000002:** Nmap SYN Port Scan (20 SYNs in 3 seconds).
*   **SID 9000003:** SSH Brute Force (5 attempts to port 22 in 60 seconds).
*   **SID 9000004:** Suspicious HTTP User-Agent (`curl`/scanner signature).
*   **SID 9000005:** TCP Flood DoS Pattern (100 SYNs in 5 seconds).

**Phase 3 — Sensor Activation & Evidence Collection**
Suricata was launched in live mode on `eth0`. Simultaneously, Wireshark was started to capture raw packets (`soc3_capture.pcapng`) to serve as an independent, impartial evidence source.

**Phase 4 — Controlled Attack Simulation**
The attack sequence was executed from the Windows host:
1.  **Reconnaissance:** `ping -n 20 192.168.56.101`
2.  **Scanning:** `nmap -sS 192.168.56.101 -p 1-1000`
3.  **Brute Force:** `for /L %i in (1,1,10) do ssh baduser@192.168.56.101`

**Phase 5 — Alert Triage & Validation**
All 250 alerts were reviewed. For an alert to be classified as a **True Positive**, the raw packet data in `soc3_capture.pcapng` had to confirm the traffic pattern (e.g., `tcp.flags.syn==1` for Nmap).

**Phase 6 — GRC Documentation**
The entire setup, attack timeline, validation results, and honest control limitations were documented and mapped to ISO 27001 Annex A, NIST CSF, and MITRE ATT&CK.

---

## 3.4 Alert Volume Summary (Sourced from `fast.log`)

**Total alerts: 250**

| Rule Fired | Count | SID |
|---|---|---|
| Nmap SYN Scan Detected | 150 | 9000002 |
| ET INFO — Kali hostname in DHCP | 39 | 2022973 |
| TCP Flood DoS Pattern | 31 | 9000005 |
| SSH Brute Force Attempt | 24 | 9000003 |
| ICMP Ping Sweep Detected | 6 | 9000001 |

---

## 3.5 Constraints & Limitations
*   **HTTP Rule Environment Gap (Honest Disclosure):** SID 9000004 (HTTP User-Agent detection) was **not triggered** during the test because the target host did not have a web server running on port 80. This is an environmental constraint, **not a control failure**. The control cannot be claimed as validated until a web server is deployed and tested.
*   **Detection Only (No Prevention):** Suricata is operating in IDS (detection) mode. It does not block the scanning traffic. An IPS (Inline) deployment or SOAR integration is required for a preventative effect.
