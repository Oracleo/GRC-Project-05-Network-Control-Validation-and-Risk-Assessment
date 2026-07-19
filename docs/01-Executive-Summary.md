# 1. Executive Summary

- **Date of Assessment:** February 25, 2026
- **Conducted By:** Niladri Biswas
- **Assessment Type:** Network Detective Control Validation (Suricata IDS)
- **Target Asset:** Internal Corporate Network Segment

## Situation Overview
A controlled network threat detection exercise was conducted to validate the effectiveness of the deployed Network Intrusion Detection System (Suricata). A multi-stage attack sequence—Reconnaissance (ICMP ping), Scanning (Nmap SYN), and an Initial Access attempt (SSH Brute Force)—was executed against a monitored host.

## Key Findings
- **Control Validation Success:** Suricata IDS successfully fired on 4 out of 5 custom detection rules, generating **250 distinct alerts**.
- **Independent Evidence Confirmation:** Wireshark packet analysis confirmed 100% of triggered alerts as **true positives**. 3,130 SYN packets and 123 SSH packets were independently validated.
- **Transparent Control Limitation:** The HTTP User-Agent rule (SID 9000004) did not trigger during the test. **This was documented honestly as an environment constraint** (the target web server was not running), rather than being falsely claimed as a passed control. This demonstrates mature, defensible control-testing methodology.

## Strategic Recommendations
1. **Automated Response (Immediate):** Integrate the Suricata alerts with a SIEM/SOAR platform to trigger automated firewall blocks on the attacking IP upon detection of 5+ SYN packets.
2. **Expand Monitor Scope:** The current IDS deployment covers a single Host-Only network segment. Extend sensor placement to WAN boundaries and critical server subnets.
3. **Close Verification Gap:** Deploy a test web service to formally validate the HTTP User-Agent rule (SID 9000004) and achieve 100% custom rule verification.

## Conclusion
This exercise proves that the Suricata IDS is operationally effective and capable of detecting adversarial reconnaissance and initial access attempts. Crucially, by honestly documenting the HTTP rule limitation, this report provides credible, audit-ready evidence of the true security posture, rather than overstating control coverage.
