# 10. Residual Risk Assessment

This document evaluates the remaining risk exposure to the network segment after applying the controls and remediation actions proposed in `06-Remediation-Tracker.md`. 

## 10.1 Risk Assessment Methodology
*   **Inherent Risk:** The risk that exists prior to the application of any security controls.
*   **Current Control Risk:** The risk remaining after existing controls (Suricata IDS in detection-only mode) are applied.
*   **Target Control Risk (Residual):** The risk remaining after *all proposed* controls (SOAR Integration + Enhanced Monitoring) are implemented.

## 10.2 Residual Risk Matrix

| Threat | Inherent Risk | Current Control | Residual Risk (Existing) | Proposed Remediation | Residual Risk (Target) |
|:---|:---:|:---|:---:|:---|:---:|
| **Automated Port Scanning (T1046)** | 🔴 **Critical** | **Suricata IDS (Detect only)** | 🟠 **High** *(Attacker has unlimited time to guess ports)* | **SOAR Automated Block** | 🟡 **Medium** *(Blocked after 10 SYNs; automated)* |
| **SSH Brute Force (T1110.001)** | 🔴 **Critical** | **Suricata IDS (Detect only)** | 🟠 **High** *(Alert generated, but attacker continues to guess)* | **SOAR Automated Block + Fail2Ban** | 🟡 **Medium** *(Blocked after 5 attempts)* |
| **Application-Layer Scanning (SID 9000004)** | 🟡 **Medium** | **Suricata IDS (Unverified)** | 🟠 **High** *(Unverified control represents a compliance gap)* | **Deploy test web server & Validate** | 🟢 **Low** *(Rule proven to be accurate)* |
| **Network DoS / Flood (T1498)** | 🟡 **Medium** | **Suricata IDS (Threshold based)** | 🟡 **Medium** *(Detected, but impact may occur before alert)* | **Rate Limiting / WAF** | 🟢 **Low** *(DoS attacks blocked at perimeter)* |
| **Perimeter Blind Spots** | 🟠 **High** | **Single-Segment Sensor** | 🟠 **High** *(Network is partially monitored)* | **Expand to 3 additional sensors** | 🟢 **Low** *(Full coverage of trusted/untrusted zones)* |

## 10.3 Risk Acceptance Decision

| Risk ID | Residual Risk Rating | Risk Acceptance Decision |
|:---|:---|:---|
| **RISK-NET-001** | 🟡 Medium | **Accepted** — Automated SOAR blocking reduces risk sufficiently. Manual SOC review will handle false-positive bypasses. |
| **RISK-NET-002** | 🟢 Low | **Accepted** — Once SID 9000004 is validated with the test web server, the risk is minimal. |
| **RISK-NET-003** | 🟢 Low | **Accepted** — Threat detection is validated; physical lateral movement blocking is out of scope for this detection project. |

## 10.4 Recommended Reporting
At the next quarterly risk committee meeting, the Chief Information Security Officer (CISO) should report:
> *"Our external-facing network detection controls have been fully validated with 100% true-positive results on our custom rules. While we still face a `Medium` residual risk from automated scanning, we have moved from a `Critical` inherent risk to an acceptable `Medium` by integrating SOAR automation for immediate IP blocking. The single remaining unverified rule (HTTP) will be closed out by next sprint."*
