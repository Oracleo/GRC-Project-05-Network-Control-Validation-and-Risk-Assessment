# 4. Assumed Business Context

To ensure a realistic GRC evaluation, this assessment assumes a specific organizational context. We are evaluating the network detection controls of **TechFin Innovations Inc.** (a simulated mid-sized financial technology firm) based on its actual business drivers, threat landscape, and risk appetite.

## 4.1 Simulated Organization Profile
| Attribute | Details |
|:---|:---|
| **Industry** | FinTech / Payroll Processing |
| **Headcount** | 800 Employees (hybrid remote) |
| **Regulatory Burden** | GDPR (EU employee data), PCI DSS v4.0 (banking integrations), SOC 2 Type II. |
| **Core Business Function** | Cloud-based HR, Payroll, and Employee Benefits Portal. |

## 4.2 Threat Landscape & Adversary Profile
*   **Threat Actor:** **Low-to-Medium Sophistication External Adversary (e.g., Hacktivist / Initial Access Broker).**
*   **Motivation:** Financial extortion, credential harvesting, and selling access to the Dark Web.
*   **Tactics, Techniques, and Procedures (TTPs):** They rely heavily on automated, widely available open-source tools (Nmap, Hydra, Nuclei). They typically operate in a "spray-and-pray" manner—scanning broad IP ranges for vulnerable or open services before deploying specific payloads.
*   **Relevance to this Engagement:** The simulated attack chain (ICMP sweep → Nmap SYN scan → SSH brute force) reflects the exact behavior of such an automated adversary. Our detection controls must effectively identify and stop this predictable, automated kill chain.

## 4.3 Business & Risk Objectives
1.  **Protect Employee PII:** The application server (`app-prod-01.internal.corp`) contains sensitive payroll data. A compromise would trigger a GDPR Article 33 (72-hour breach notification) event.
2.  **Maintain Operational Uptime:** DoS patterns (SID 9000005) against the payroll portal must be detected during payroll processing windows to prevent employee dissatisfaction and operational delays.
3.  **Achieve Defensible Compliance:** The organization must prove to auditors (ISO 27001, PCI DSS) that network monitoring controls are not just *documented*, but actively *tested and validated* to be operational.

## 4.4 Risk Appetite Statement
> *"TechFin Innovations adopts a **moderate risk appetite**. We will accept low/medium residual risk if the cost of full mitigation outweighs the potential financial impact of a breach. However, **we have zero tolerance for unverified control gaps**—all detective controls must be tested and proven to trigger under live conditions."*

This statement explains why the **unverified HTTP rule (SID 9000004)** is flagged as a critical follow-up action in our remediation tracker. It is currently an unacceptable gap against our stated risk appetite.
