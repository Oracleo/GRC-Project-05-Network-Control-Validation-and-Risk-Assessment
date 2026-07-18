# 3. Risk Register & Business Impact Analysis

This register serves as the master document for tracking the identified network threat and the control gap discovered during validation.

## Risk Scoring Context
*   **Asset:** Internal network segment hosting a critical application server.
*   **Threat:** External or internal adversary performing reconnaissance and scanning to identify vulnerable services.
*   **Vulnerability:** Absent automated blocking response (IDS-only deployment); HTTP-layer monitoring unverified.

## Risk Register Entries
| Priority | Vulnerability / Threat | Likelihood | Business Impact | Risk Score | Recommended Owner |
|:---:|:---|:---|:---|:---|:---|
| **P1** | **Absent Automated Blocking (IDS-only deployment)** | Very High (Attackers routinely scan; without automation, they have unlimited time) | **Critical:** If a scanning threat is not blocked automatically, the adversary can pivot to exploit a vulnerable service. | Critical | Security Engineer / SOC Lead |
| **P2** | **Unverified HTTP-Layer Detection (Rule 9000004)** | High (HTTP is a primary attack vector) | **Medium:** A gap in detection leaves HTTP-based reconnaissance (e.g., directory busting, API scans) unmonitored. | High | Web App Admin / Security Team |
| **P2** | **Single Segment Monitoring Scope** | Medium (Attackers may operate from other subnets) | **Medium:** Single-segment detection creates a false sense of total network coverage. | Medium | Network Architect |
