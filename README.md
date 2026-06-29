# Network Threat Detection & Audit Evidence Collection | Suricata + Wireshark

![Status](https://img.shields.io/badge/Status-Completed-28A745?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=linux&logoColor=white)
![Suricata](https://img.shields.io/badge/Suricata-8.0.3-orange?style=for-the-badge)
![Wireshark](https://img.shields.io/badge/Wireshark-Live%20Capture-1679A7?style=for-the-badge&logo=wireshark)
![Alerts](https://img.shields.io/badge/Alerts%20Triaged-249-red?style=for-the-badge)
![Framework](https://img.shields.io/badge/Framework-ISO%2027001%20%7C%20NIST%20CSF-0057A8?style=for-the-badge)
![Kill Chain](https://img.shields.io/badge/Mapped-Cyber%20Kill%20Chain-blueviolet?style=for-the-badge)

**A structured network threat detection exercise using Suricata IDS and Wireshark to monitor, triage, and document a multi-stage attack sequence — producing audit-grade evidence and control-testing documentation mapped to the cyber kill chain, ISO 27001, and NIST CSF.**

---

## 📋 Table of Contents

1. [GRC Relevance — Why This Project](#-grc-relevance--why-this-project)
2. [Incident Summary](#-incident-summary)
3. [Lab Environment](#-lab-environment)
4. [Tools & Environment](#-tools--environment)
5. [Implementation — Step by Step](#-implementation--step-by-step)
6. [Custom Detection Rules — Control Design](#-custom-detection-rules--control-design)
7. [Attack Scenarios & Control Testing](#-attack-scenarios--control-testing)
8. [Alert Triage & Evidence Validation](#-alert-triage--evidence-validation)
9. [Packet-Level Audit Evidence](#-packet-level-audit-evidence)
10. [Incident Report — Kill Chain Mapping](#-incident-report--kill-chain-mapping)
11. [Risk Register Entry & Control Recommendations](#-risk-register-entry--control-recommendations)
12. [GRC Concepts Applied](#-grc-concepts-applied)
13. [Repository Structure](#-repository-structure)

---

## 🎯 GRC Relevance — Why This Project

Network detection control testing is a core GRC audit activity. An organisation can document a network monitoring policy on paper, but a GRC analyst's job is to verify the control is actually operating effectively — that detection rules fire as designed, that alerts are not false positives or false negatives, and that the evidence trail supports both incident response and compliance audit requests. A GRC analyst evaluating a network monitoring programme is expected to:

- Verify detective controls are configured correctly and operating as intended
- Validate alerts against raw evidence to confirm true positive classification
- Trace a multi-stage attack against a recognised reference model (cyber kill chain)
- Produce control-testing documentation suitable for internal audit and external assessor review
- Identify control gaps and limitations honestly, rather than overstating coverage
- Recommend remediation aligned to the organisation's risk appetite and compliance obligations

This project simulates that control-testing exercise end-to-end. **Suricata 8.0.3** was deployed as the network IDS with the **Emerging Threats ruleset (48,701 rules)** plus **5 custom detection rules**, monitoring a live lab network. A simulated multi-stage attack — reconnaissance, port scanning, and brute force — was generated and the resulting **249 alerts** were triaged against **Wireshark packet-level evidence (8,344 packets)** to confirm true positive classification.

**Outcome: All four custom rules associated with executed attacks fired correctly and were confirmed as true positives through packet-level forensic validation. One rule limitation was identified and documented honestly as an environment constraint, not a false control claim. A structured incident report was produced mapping the attack sequence to the cyber kill chain.**

> 💡 **GRC Context:** ISO 27001 Annex A.8.16 (Monitoring activities) and A.5.24 (Incident management planning) require organisations to monitor for and respond to security events with documented evidence. NIST CSF DE.AE-2 (Detected events are analysed) and DE.DP-4 (Event detection information is communicated) both require this exact validation-and-documentation workflow. This project demonstrates control deployment, control testing, and the audit evidence trail those controls require — including honest documentation of a detection limitation, which is itself a hallmark of credible control testing.

---

## 📊 Incident Summary

| Field | Detail |
|---|---|
| **Incident ID** | INC-2026-001 |
| **Attack Pattern** | Multi-stage — Reconnaissance → Port Scan → Service Brute Force |
| **Source** | 192.168.56.107 (lab-simulated attacker) |
| **Target** | 192.168.56.101 (monitored sensor host) |
| **Total Alerts** | 249 |
| **True Positive Rate** | 4 of 5 custom rules confirmed true positive; 1 rule limitation documented |
| **Packets Captured** | 8,344 |
| **Severity** | High |
| **Status** | ✅ Confirmed Attack Sequence — Control Validated |
| **Kill Chain Stages Observed** | Reconnaissance, Scanning, Initial Access Attempt |
| **ISO 27001 Controls** | A.8.16, A.5.24, A.8.7 |
| **NIST CSF** | DE.AE-2, DE.DP-4, DE.CM-1 |

---

## 🏗️ Lab Environment

| Component | Details |
|---|---|
| Host OS | Windows 10 (10.0.19045.2965) |
| VM Platform | Oracle VirtualBox |
| Guest OS | Kali Linux 2026-W06 |
| Network Mode | Host-Only Adapter |
| Sensor / Monitored Host | Kali Linux — 192.168.56.101 |
| Simulated Source | Windows 10 — 192.168.56.107 |
| Monitored Interface | eth0 |

```
┌─────────────────────┐         ┌────────────────────────────┐
│   Windows 10 Host   │         │      Kali Linux VM         │
│   192.168.56.107    │◄───────►│      192.168.56.101        │
│   (Simulated Source)│ eth0    │   Suricata IDS + Wireshark │
└─────────────────────┘         └────────────────────────────┘
         VirtualBox Host-Only Network: 192.168.56.0/24
```

---

## 🧰 Tools & Environment

| Tool | Version | Purpose |
|---|---|---|
| Suricata | 8.0.3 | Network IDS — detection control under test |
| Wireshark | Latest | Packet capture — independent evidence source for validation |
| Emerging Threats Ruleset | 48,701 rules | Community detection signatures |
| Custom Rules | 5 rules | Control-specific detections targeting the test attack pattern |
| Nmap | 7.98 | Port scan simulation |
| OpenSSH Client | Windows built-in | SSH brute force simulation |
| curl | Windows built-in | HTTP scanner simulation |

---

## 🔧 Implementation — Step by Step

### Phase 1 — IDS Deployment & Configuration

```bash
sudo apt update && sudo apt install -y suricata
suricata --version
# Output: Suricata 8.0.3 ("undefined")
```

![Suricata Version Verified](screenshots/GRC5-01-suricata-version-verified.png)

`HOME_NET` and monitored interface configured in `suricata.yaml`:

```yaml
vars:
  address-groups:
    HOME_NET: "[192.168.56.0/24]"

af-packet:
  - interface: eth0
```

![Suricata YAML Config](screenshots/GRC5-02-suricata-yaml-homenet-afpacket.png)

> **GRC Note:** Correct `HOME_NET` scoping is a control design decision with direct audit relevance. An incorrectly scoped HOME_NET produces either excessive false positives (too broad) or missed detections on internal lateral movement (too narrow). This configuration step is exactly the kind of control parameter an auditor would request justification for.

Ruleset updated — **64,554 rules downloaded, 48,701 enabled, 0 failed**:

![Suricata Rules Updated](screenshots/GRC5-03-suricata-rules-updated.png)

Configuration validated — **48,701 rules successfully loaded, 0 failed**:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v 2>&1 | tail -20
```

![Suricata Config Test Passed](screenshots/GRC5-04-suricata-config-test-passed.png)

---

### Phase 2 — Custom Detection Rule Deployment

Five custom rules deployed to `/etc/suricata/rules/local.rules`, targeting the specific attack patterns to be tested. Configuration validated post-deployment:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v 2>&1 | grep "rules successfully"
# Info: detect: 2 rule files processed. 48706 rules successfully loaded, 0 rules failed
```

![Custom Rules Validated](screenshots/GRC5-05-custom-rules-validated.png)

---

### Phase 3 — Sensor Activation

Suricata launched live on the monitored interface:

```bash
sudo suricata -c /etc/suricata/suricata.yaml -i eth0 -l /var/log/suricata/
```

![Suricata Running Live](screenshots/GRC5-06-suricata-running-live.png)

Wireshark started in parallel as an **independent evidence source** — capturing raw packets without dependency on Suricata's interpretation, providing the cross-validation needed to confirm alerts are genuine and not configuration artefacts.

![Wireshark Capturing eth0](screenshots/GRC5-07-wireshark-capturing-eth0.png)

---

### Phase 4 — Controlled Attack Traffic Generation

A controlled, multi-stage attack sequence was generated from the Windows host to test detection coverage against the lab sensor.

![Attack Traffic Generated](screenshots/GRC5-08-attack-traffic-generated.png)

---

## 🛡️ Custom Detection Rules — Control Design

```
# Rule 1: ICMP Ping Sweep (Reconnaissance)
alert icmp any any -> $HOME_NET any (
  msg:"GRC-LAB ICMP Ping Sweep Detected";
  itype:8;
  threshold:type threshold, track by_src, count 5, seconds 10;
  sid:9000001; rev:1;)

# Rule 2: Nmap SYN Port Scan
alert tcp any any -> $HOME_NET any (
  msg:"GRC-LAB Nmap SYN Scan Detected";
  flags:S;
  threshold:type threshold, track by_src, count 20, seconds 3;
  sid:9000002; rev:1;)

# Rule 3: SSH Brute Force Detection
alert tcp any any -> $HOME_NET 22 (
  msg:"GRC-LAB SSH Brute Force Attempt";
  flags:S;
  threshold:type threshold, track by_src, count 5, seconds 60;
  sid:9000003; rev:1;)

# Rule 4: Suspicious HTTP User-Agent (curl/scanner)
alert http any any -> $HOME_NET any (
  msg:"GRC-LAB Suspicious HTTP User-Agent Detected";
  http.user_agent; content:"curl";
  sid:9000004; rev:1;)

# Rule 5: TCP Flood / DoS Pattern
alert tcp any any -> $HOME_NET any (
  msg:"GRC-LAB TCP Flood DoS Pattern";
  flags:S;
  threshold:type threshold, track by_src, count 100, seconds 5;
  sid:9000005; rev:1;)
```

**Rule Design Rationale — Documented Risk-Based Thresholds:**

| SID | Threshold Logic | Risk-Based Justification |
|---|---|---|
| 9000001 | 5 ICMP in 10 seconds | Normal ping is 1–4 packets; a sweep produces a rapid burst |
| 9000002 | 20 SYN in 3 seconds | Automated scanners send hundreds of SYNs per second; legitimate traffic does not |
| 9000003 | 5 SYN to port 22 in 60 seconds | Legitimate SSH retries are slow; 5+ in this window indicates an automated tool |
| 9000004 | Any curl User-Agent observed | curl is not a browser; its presence on a monitored segment indicates scripted access |
| 9000005 | 100 SYN in 5 seconds | Sustained high-rate SYN traffic indicates denial-of-service intent |

> **GRC Note:** Each threshold here represents a documented, justifiable trade-off between detection sensitivity and false-positive volume. This is precisely the kind of control parameter justification an internal auditor or external assessor will request when testing a detective control — "why this number, and what is the basis for it?"

---

## 🎯 Attack Scenarios & Control Testing

### Test 1 — ICMP Reconnaissance (Control: SID 9000001)

```cmd
ping 192.168.56.101 -n 20
```

**Result:** 20 ICMP Echo Requests sent, all replied. TTL=64 confirmed Linux target.

---

### Test 2 — Nmap SYN Port Scan (Control: SID 9000002)

```cmd
nmap -sS 192.168.56.101 -p 1-1000
```

**Result:** 1,000 ports scanned in 3.20 seconds. All ports closed or filtered — but every SYN packet was captured by the sensor, confirming detection coverage regardless of scan outcome.

---

### Test 3 — SSH Brute Force Simulation (Control: SID 9000003)

```cmd
for /L %i in (1,1,10) do ssh -o ConnectTimeout=2 baduser@192.168.56.101
```

**Result:** Connection refused/timed out responses — SSH service was not active on the target, but TCP SYN packets reached the sensor. 9 alert events captured, confirming the control fires independent of the target service's actual availability.

---

### Test 4 — Suspicious HTTP User-Agent (Control: SID 9000004)

```cmd
curl -A "curl/7.68.0" http://192.168.56.101
```

**Result:** Port 80 not open on the target — connection refused at the TCP layer before the HTTP application layer was reached. Rule did not fire.

> **GRC Note — Documenting a Control Limitation Honestly:** This is the most important finding in the entire exercise from a GRC perspective. The rule is correctly written. It did not fire because the test environment lacked a running web service on port 80 — an environment constraint, not a rule defect. A credible control test report distinguishes between "the control failed" and "the control was not exercised under the conditions required to trigger it." Misrepresenting this distinction — either by omitting it or by falsely claiming the rule fired — would constitute inaccurate audit evidence. Honest documentation of this limitation is itself evidence of sound control-testing methodology.

---

## 📊 Alert Triage & Evidence Validation

### Alert Volume Summary — fast.log

**Total alerts: 249**

| Rule Fired | Count | SID |
|---|---|---|
| Nmap SYN Scan Detected | 156 | 9000002 |
| TCP Flood DoS Pattern | 30 | 9000005 |
| SSH Brute Force Attempt | 24 | 9000003 |
| ICMP Ping Sweep Detected | 2 | 9000001 |
| ET INFO — Kali hostname in DHCP | 37 | 2022973 |

![Suricata fast.log Alerts](screenshots/GRC5-09-suricata-fast-log-alerts.png)

### Structured Alert Evidence — eve.json

Machine-readable, SIEM-ingestible alert data was also captured, providing the structured evidence format required for integration with downstream GRC and SIEM tooling:

```json
{
  "timestamp": "2026-02-25T11:18:53.852420-0500",
  "flow_id": 456057548001102,
  "in_iface": "eth0",
  "event_type": "alert",
  "src_ip": "192.168.56.107",
  "dest_ip": "192.168.56.101",
  "proto": "ICMP",
  "alert": {
    "signature_id": 9000001,
    "signature": "GRC-LAB ICMP Ping Sweep Detected",
    "severity": 3
  },
  "community_id": "1:aWp7QzSXFNVZZcb8gtTDuL+7ipM="
}
```

![Suricata eve.json Alerts](screenshots/GRC5-10-suricata-eve-json-alerts.png)

### Alert Triage — True Positive Validation

Every alert was cross-validated against independent Wireshark packet evidence before being classified — the core principle of credible alert triage in any audit or incident context.

| Alert | Classification | Supporting Evidence |
|---|---|---|
| ICMP Ping Sweep | ✅ True Positive | Wireshark shows 20 ICMP Echo Requests from source host |
| Nmap SYN Scan | ✅ True Positive | 3,130 SYN-only packets in capture, distributed across distinct ports |
| SSH Brute Force | ✅ True Positive | 123 packets to port 22, incrementing source ports confirm automated tool |
| TCP Flood / DoS | ✅ True Positive | Co-occurred with Nmap scan — same high-volume SYN burst pattern |
| HTTP User-Agent | ⚠️ Not Triggered | Documented environment limitation — no web server on target; not a control failure |
| ET Kali DHCP | ℹ️ Informational | Hostname fingerprinted via DHCP broadcast — flagged for awareness, not severity-rated |

**Why this matters for GRC:** Four of five purpose-built controls were independently validated as true positives using a second, unrelated evidence source. This is the audit standard for control testing — a control is not considered verified merely because it produced an alert; the alert must be corroborated against independent evidence.

---

## 🔍 Packet-Level Audit Evidence

All packet evidence preserved in `grc5_capture.pcapng` — **8,344 total packets**, retained as the primary audit evidence artefact for this control test.

### Evidence 1 — ICMP Reconnaissance Validation

**Filter:** `icmp && ip.src == 192.168.56.107` — **34 packets displayed**

Sequential ICMP Echo Requests with incrementing sequence numbers confirmed. TTL=128 (Windows default) independently confirms the source host identity.

![Wireshark ICMP Filter](screenshots/GRC5-12-wireshark-filter-icmp-ping.png)

### Evidence 2 — Port Scan Validation

**Filter:** `tcp.flags.syn == 1 && tcp.flags.ack == 0 && ip.src == 192.168.56.107` — **3,130 packets displayed (37.5% of total capture)**

Every packet exhibits the `[SYN]` flag with `Seq=0 Win=1024 Len=0 MSS=1460` — the recognised Nmap SYN scan signature. A single source port connecting to hundreds of distinct destination ports independently confirms automated scanning behaviour rather than normal application traffic.

![Wireshark Nmap SYN Filter](screenshots/GRC5-13-wireshark-filter-nmap-syn-scan.png)

### Evidence 3 — Brute Force Pattern Validation

**Filter:** `tcp.dstport == 22 && ip.src == 192.168.56.107` — **123 packets displayed**

Source ports increment linearly (9770, 9772, 9773, 9774...) — each representing a discrete connection attempt. Repeated connection teardown and restart patterns independently confirm an automated, scripted login tool rather than manual user activity.

![Wireshark SSH Filter](screenshots/GRC5-14-wireshark-filter-ssh-bruteforce.png)

### Evidence 4 — Protocol Hierarchy Anomaly

**TCP: 75% (6,260 packets) — ICMP: 0.8% (68 packets) — remaining: normal background traffic**

TCP dominance at this proportion on an otherwise quiet network segment is itself an anomaly indicator independent of any specific alert — a finding that would warrant investigation even without rule-based detection.

![Wireshark Protocol Hierarchy](screenshots/GRC5-15-wireshark-protocol-hierarchy.png)

### Evidence 5 — Connection Pattern Analysis

**3,130 TCP conversations** between the two test hosts — all exhibiting a single packet exchanged in each direction (SYN → RST/ACK), confirming incomplete handshakes consistent with scanning rather than legitimate service connections.

![Wireshark TCP Conversations](screenshots/GRC5-16-wireshark-conversations-tcp.png)

---

## 📋 Incident Report — Kill Chain Mapping

```
╔══════════════════════════════════════════════════════════════╗
║         CONTROL TEST & INCIDENT REPORT — INC-2026-001        ║
╚══════════════════════════════════════════════════════════════╝

Date/Time:      2026-02-25, 11:18 – 11:35 UTC-5
Severity:       HIGH
Status:         True Positive — Confirmed Attack Sequence
Sensor:         Suricata 8.0.3 on eth0 (192.168.56.101)

EXECUTIVE SUMMARY
A controlled, multi-stage attack sequence was executed against a
monitored sensor host to test network detection control coverage.
The sequence followed the classic kill chain pattern:

    Reconnaissance → Scanning → Initial Access Attempt

All stages were detected by purpose-built Suricata rules and
independently confirmed via Wireshark packet-level evidence.

ATTACK TIMELINE — MAPPED TO CYBER KILL CHAIN

11:18:49  RECONNAISSANCE
          SID 9000001 fired — 20 ICMP Echo Requests
          Kill Chain Stage 1: Reconnaissance

11:22:51  SCANNING
          SID 9000002 fired — 156 alerts over 1.69 seconds
          SID 9000005 co-fired — 30 high-volume pattern alerts
          3,130 SYN packets independently confirmed in Wireshark
          Kill Chain Stage 2: Scanning / Weaponization preparation

11:34:38  INITIAL ACCESS ATTEMPT
          SID 9000003 fired — 9 attempts over ~90 seconds
          Source ports incrementing — automated tool signature
          Kill Chain Stage 3: Initial Access / Exploitation attempt

CONTROL EFFECTIVENESS SUMMARY
Total packets captured:    8,344
Custom rules tested:       5
Rules confirmed effective: 4 (true positive, independently validated)
Rules not triggered:       1 (documented environment limitation)
False positives observed:  0

RECOMMENDED RESPONSE ACTIONS
1. Block source host at the perimeter firewall
2. Review authentication logs on target host for any successful logins
3. Verify no persistence mechanisms were established
4. Run integrity verification on sensitive target host files
5. Escalate for broader subnet threat hunting if in production
6. Add source indicators to threat intelligence watchlist
```

---

## 📋 Risk Register Entry & Control Recommendations

### Risk Register Entry

| Field | Value |
|---|---|
| **Risk ID** | RISK-NET-003 |
| **Asset** | Internal network segment / monitored hosts |
| **Threat** | External or internal reconnaissance and scanning activity preceding intrusion |
| **Vulnerability** | Network exposed to scanning; detection control coverage incomplete for application-layer threats without active services |
| **Likelihood** | Medium-High — scanning activity is a routine precursor to targeted attacks |
| **Impact** | High if scanning leads to successful exploitation of a discovered service |
| **Inherent Risk** | High |
| **Current Controls** | Suricata IDS with 48,701 community rules + 5 custom rules — confirmed operational for 4 of 5 tested scenarios |
| **Control Gap** | HTTP-layer detection rule unverified due to absence of test web service; coverage limited to single monitored segment |
| **Residual Risk** | Medium |
| **ISO 27001** | A.8.16, A.5.24, A.8.7 |
| **NIST CSF** | DE.AE-2, DE.DP-4, DE.CM-1 |

---

### Control Recommendations

**Immediate — Close the Verification Gap:**
Deploy a test web service to validate Rule 9000004 (HTTP User-Agent detection) under conditions that allow the application layer to be reached. An unverified control cannot be claimed as effective in an audit — this gap should be closed before reporting full control coverage.

Maps to ISO 27001 A.8.16.

**Short-term — Expand Monitoring Coverage:**
This deployment monitors a single network segment. Extend sensor placement to cover additional segments, particularly any boundary between trusted and untrusted zones, to avoid a false sense of comprehensive coverage based on single-segment testing.

Maps to NIST CSF DE.CM-1.

**Medium-term — Automate Alert-to-Response Workflow:**
Alerts currently generate log entries reviewed manually. Integrate with a SIEM or SOAR platform to enable automated correlation, prioritisation, and ticketing — reducing analyst response time and providing a documented response timeline for audit purposes.

Maps to ISO 27001 A.5.24 and NIST CSF DE.DP-4.

**Ongoing — Maintain Control Testing Discipline:**
Repeat this style of controlled control-testing exercise periodically, and explicitly document any rule that does not fire as expected, along with the reason. Distinguishing genuine control failures from environment-specific non-triggers is essential to maintaining credible, audit-defensible control effectiveness claims over time.

Maps to ISO 27001 A.5.24.

---

## 📚 GRC Concepts Applied

| Concept | Application in This Project |
|---|---|
| Detective Control Testing | 5 custom rules deployed and tested against simulated attack scenarios |
| Independent Evidence Validation | Wireshark used as a second, independent source to confirm Suricata alerts |
| True Positive / False Positive Triage | All 249 alerts classified against packet-level evidence before being reported as confirmed |
| Honest Control Limitation Reporting | HTTP rule non-trigger documented as an environment constraint, not concealed or misrepresented |
| Cyber Kill Chain Mapping | Multi-stage attack mapped to Reconnaissance → Scanning → Initial Access |
| Risk Register Entry | RISK-NET-003 produced in GRC risk register format |
| Audit Evidence Preservation | Full packet capture (8,344 packets) retained as primary audit artefact |
| Threshold Justification | Each detection rule's threshold documented with a risk-based rationale |
| ISO 27001 Mapping | A.8.16, A.5.24, A.8.7 mapped to findings and recommendations |
| NIST CSF Mapping | DE.AE-2, DE.DP-4, DE.CM-1 mapped to control testing outcomes |
| Coverage Gap Analysis | Single-segment monitoring identified as a scope limitation requiring remediation |

### Key Questions to Prepare From This Project

**Q: Why does this project validate alerts using two separate tools instead of trusting the IDS alone?**
An IDS alert reflects the IDS's interpretation of traffic against its ruleset — it can be misconfigured, the rule logic can be flawed, or the alert can be a false positive. Cross-validating against an independent packet capture tool confirms the underlying traffic actually matches the claimed pattern. In a GRC audit context, this is the same principle as requiring independent corroboration of evidence before accepting a control as verified.

**Q: How would you report the HTTP rule that did not fire to an audit committee?**
Transparently and specifically: the rule logic was reviewed and confirmed correct, but the test could not exercise it because no service was available at the application layer the rule inspects. This is reported as an unverified control requiring further testing, not as either a passed or failed control. Concealing this distinction would misstate the actual assurance level achieved.

**Q: What is the cyber kill chain and why map findings to it?**
The cyber kill chain is a model describing the stages an attacker progresses through — reconnaissance, weaponisation, delivery, exploitation, installation, command and control, and actions on objectives. Mapping an incident to this model helps a GRC analyst communicate where in the attack lifecycle the organisation's controls successfully detected or interrupted the activity, which informs prioritisation of remediation — interrupting an attack early in the chain is significantly more cost-effective than after exploitation.

**Q: Why is the rule threshold (e.g. 20 SYN packets in 3 seconds) something a GRC analyst should be able to discuss?**
Detection thresholds represent a documented risk decision — the trade-off between catching attacks quickly and generating excessive false positives that overwhelm analyst capacity. An auditor reviewing this control will ask why this specific number was chosen. Being able to articulate the underlying rationale, as documented in this project, demonstrates the kind of control-design literacy a GRC analyst is expected to bring, even without writing the rules themselves.

**Q: What would you tell an auditor who asks if this network is "fully monitored"?**
No — this exercise validated detection on a single network segment under specific test conditions. A defensible answer to an auditor must scope the claim accurately: which segments, which protocols, under what conditions, and which controls remain unverified. Overstating coverage is a common and serious audit finding; this project's documentation deliberately avoids that by stating the coverage gap explicitly in the control recommendations.

---

## 📸 Screenshots Index

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
| 09 | GRC5-09-suricata-fast-log-alerts.png | fast.log — 249 total alerts summary |
| 10 | GRC5-10-suricata-eve-json-alerts.png | eve.json — structured SIEM-ingestible alert data |
| 11 | GRC5-11-wireshark-save-capture.png | Packet capture saved — grc5_capture.pcapng |
| 12 | GRC5-12-wireshark-filter-icmp-ping.png | ICMP reconnaissance — 34 packets, TTL=128 confirmed |
| 13 | GRC5-13-wireshark-filter-nmap-syn-scan.png | Nmap SYN scan — 3,130 packets, Win=1024 signature |
| 14 | GRC5-14-wireshark-filter-ssh-bruteforce.png | SSH brute force — 123 packets, incrementing source ports |
| 15 | GRC5-15-wireshark-protocol-hierarchy.png | Protocol hierarchy — TCP 75% anomaly indicator |
| 16 | GRC5-16-wireshark-conversations-tcp.png | TCP conversations — 3,130 incomplete handshake pattern |

---

## 📁 Repository Structure

```
GRC5-Network-Audit-Evidence-Suricata-Wireshark/
│
├── README.md                          ← Complete control test documentation
│
├── rules/
│   └── local.rules                    ← 5 custom Suricata detection rules
│
├── logs/
│   ├── fast.log                       ← Suricata human-readable alert log
│   └── eve.json                       ← Suricata structured JSON alert log
│
├── pcap/
│   └── grc5_capture.pcapng            ← Full Wireshark packet capture (8,344 packets)
│
└── screenshots/                       ← 16 annotated evidence screenshots
    ├── GRC5-01-suricata-version-verified.png
    ├── GRC5-02 through GRC5-16 ...
```

---

## ⚖️ Disclaimer

> This exercise was conducted entirely within a **controlled, isolated VirtualBox lab environment** using two virtual machines under the author's own control. All attack traffic was generated by the author against the author's own lab infrastructure for educational and professional development purposes. No external networks, systems, or third parties were involved. The methodologies demonstrated should only be applied within authorised organisational environments or designated security testing platforms with explicit written permission.

---

<div align="center">

*GRC5 · Suricata · Wireshark · Emerging Threats Ruleset · Cyber Kill Chain*

*Network Threat Detection · Control Testing · Audit Evidence · ISO 27001 · NIST CSF*

</div>
