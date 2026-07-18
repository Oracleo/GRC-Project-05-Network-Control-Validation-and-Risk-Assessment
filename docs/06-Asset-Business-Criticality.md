# 6. Asset Business Criticality & Context Analysis

In a professional GRC environment, a ping sweep against a lab VM is different from one against a production server. For this risk engagement, we contextualize the **Monitored Kali Host (`192.168.56.101`)** as follows:

## 6.1 Asset Profile
| Asset Attribute | Assigned Context |
|:---|:---|
| **Hostname** | `app-prod-01.internal.corp` |
| **Business Function** | Internal Enterprise Web Application Server (Hosting an HR portal). |
| **Data Sensitivity** | **High.** Hosts employee PII, time-tracking data, and payroll reports. |
| **Regulatory Footprint** | Subject to **GDPR** (EU employee data). |
| **Operational Criticality** | Mission-critical. If this server is compromised via an exploit discovered during an Nmap scan, employees lose access to the payroll portal. |

## 6.2 Impact Analysis Matrix
| Threat Scenario | Operational Impact | Compliance Impact | Financial/Reputational Impact |
|:---|:---|:---|:---|
| **Network Scanning (T1046) leading to Successful RCE** | Unauthorized access to HR database and defacement of the portal. | Violates GDPR Article 32 if PII is exfiltrated. | **~$500,000+** direct financial loss and severe loss of employee trust. |
| **Unblocked Adversary Reconnaissance** | Attackers have an unrestricted window of time to map the network and find weak points. | Violates NIST DE.CM-1 (Continuous monitoring). | Harder to detect lateral movement, leading to an eventual data breach. |

## 6.3 Conclusion on Priority Scoring
The lack of automated blocking on the IDS is escalated to a **Critical P1** because an unblocked scanner can immediately move from Reconnaissance to Exploitation in a matter of seconds. The priority matrix in `03-Risk-Register.md` is driven by the criticality of the HR data on this specific host.
