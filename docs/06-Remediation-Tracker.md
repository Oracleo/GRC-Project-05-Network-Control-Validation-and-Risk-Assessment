# 6. Remediation & Treatment Plan Tracker

This artifact tracks the remediation plan with a **Cost-Benefit Analysis** to justify security spending.

## Cost-Benefit Context
*   **Cost of Inaction (Breach):** An undetected or unblocked port scan that leads to a vulnerable service compromise can cost an organization an average of **$100,000 - $500,000+** in incident response, data recovery, and legal fees.
*   **Cost of Remediation:** Enabling automated blocking (SOAR/IPS) is a configuration and integration task, typically **< $500** in engineering time. Closing the HTTP rule gap requires spinning up a test web server (~$50 in cloud costs).

| Priority | Finding | Remediation Action | Assigned To | Due Date | Remediation Cost (Effort) | Cost of Inaction | Status |
|:---:|:---|:---|:---|:---:|:---|:---|:---|
| **P1** | **Absent Automated Blocking** | 1. Integrate Suricata with a SOAR or SIEM platform.<br>2. Configure an automation rule to block the source IP (via firewall) after 10+ SYN packets are detected. | SOC Lead | **Immediate** | **~$300** (1 Engineer hour) | **~$100,000+** (Ransomware/Breach) | Not Started |
| **P2** | **Unverified HTTP Rule** | 1. Deploy a simple Python/Flask web server on the Kali target.<br>2. Re-execute the `curl` test to verify SID 9000004 fires correctly. | AppSec Engineer | 7 Days | **~$50** (Time and config) | **~$50,000** (Missed API reconnaissance) | In Progress |
| **P2** | **Expand Monitoring Scope** | 1. Deploy additional Suricata sensors at the network perimeter and critical server subnets. | Network Admin | 30 Days | **~$300** (Additional VM setup) | **~$500,000** (Full network compromise) | Not Started |

## Budget Justification Summary
The **P1** and **P2** remediation actions represent a total estimated investment of **~$650**. Failure to implement these controls could result in a breach far exceeding **$100,000**. 
**GRC Recommendation:** Immediate funding is requested for the SOAR integration to reduce the residual risk of network scanning to an acceptable level.
