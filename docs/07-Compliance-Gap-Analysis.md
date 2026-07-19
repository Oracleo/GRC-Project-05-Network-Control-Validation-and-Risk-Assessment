# 7. Compliance Gap Analysis (ISO 27001, NIST CSF, PCI DSS)

This document maps the identified security control implementation to specific regulatory frameworks.

## ISO 27001:2022 Annex A Mappings
| Control ID | Control Name | Gap Evidence | Compliance Status |
|:---:|:---|:---|:---:|
| **A.8.16** | Monitoring Activities | Suricata 8.0.3 with 48,701 rules is deployed and actively monitoring the network segment. | ✅ **Compliant** |
| **A.8.7** | Protection against malware | IDS detection of scanning activity assists in identifying pre-cursors to malware delivery. | 🟡 **Control Gap** |
| **A.5.24** | Incident management planning | Alerts are generated and logged, but not yet automated into a ticketing system for formal incident response. | 🟡 **Control Gap** |

## NIST Cybersecurity Framework (CSF) Mappings
| NIST Function | NIST Category | Gap Evidence |
|:---|:---|:---|
| **DETECT (DE)** | DE.CM-1 (Network is monitored) | Suricata IDS is actively monitoring `eth0` and generating 249 alerts. |
| **DETECT (DE)** | DE.AE-2 (Detected events are analyzed) | Alerts were manually triaged and correlated with Wireshark packet evidence to confirm true positives. |
| **RESPOND (RS)** | RS.CO-2 (Response is coordinated) | The alert system is not currently integrated with automated firewall blocking or ticketing. |
| **IDENTIFY (ID)** | ID.RA-1 (Asset vulnerabilities) | The control testing exercise identified the absence of a web server to test the HTTP detection rule. |

## PCI DSS v4.0 Mappings (Relevance for Financial Data)
*   **Requirement 10.2:** Audit trails for system components must be enabled and active. (Passed: Wireshark PCAP and Suricata logs are retained).
*   **Requirement 11.5.1:** Intrusion detection and prevention mechanisms must be monitored. (Passed: Suricata is active; alerts are generated).
*   **Requirement 8.5:** The use of group, shared, or generic IDs must be prevented. (Not addressed, but the SSH brute force detection highlights this as a relevant control).

## GDPR (General Data Protection Regulation) Implications
If the scanned server contained PII, an initial reconnaissance scan is the first stage of a data breach. Failure to deploy effective IDS/IPS controls would be viewed as a failure to comply with **Article 32 (Security of Processing)**.
