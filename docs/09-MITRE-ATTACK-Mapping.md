# 9. MITRE ATT&CK Threat Actor Mapping

Mapping the multi-stage attack to the MITRE ATT&CK framework allows us to visualize the adversary's full kill chain and apply precise, actionable mitigations. This mapping validates that our custom Suricata rules target the exact techniques utilized by modern automated adversaries.

## Tactical Kill-Chain Mapping
| Vulnerability / Threat | MITRE Tactic | MITRE Technique ID / Technique Name | Adversary Action Enabled |
|:---|:---|:---|:---|
| **ICMP Ping Sweep (SID 9000001)** | **TA0043 (Reconnaissance)** | **T1595.001** / Active Scanning: Scanning IP Blocks | The attacker validates the target host is alive, confirming it as a viable attack candidate. |
| **Nmap SYN Port Scan (SID 9000002)** | **TA0007 (Discovery)** | **T1046** / Network Service Discovery | The attacker maps out open ports to identify potential entry points (e.g., SSH, HTTP). |
| **SSH Brute Force Attempt (SID 9000003)** | **TA0006 (Credential Access)** | **T1110.001** / Brute Force: Password Guessing | The attacker automatically attempts to guess valid SSH credentials to gain initial access. |
| **HTTP `curl` User-Agent (SID 9000004)** | **TA0043 (Reconnaissance)** | **T1595.002** / Active Scanning: Vulnerability Scanning | The attacker utilizes a scripted scanner to profile the web application layer for vulnerabilities. *(Note: Unverified in this test cycle due to environmental constraints).* |
| **TCP Flood / DoS (SID 9000005)** | **TA0040 (Impact)** | **T1498** / Network Denial of Service | The attacker attempts to overwhelm network bandwidth and disrupt operations. |

---

## Attack Chain Scenario (The "Real World" Threat)

1.  **Reconnaissance (TA0043 - T1595.001):** The attacker executes an ICMP ping sweep against the target network to find live hosts.
2.  **Discovery (TA0007 - T1046):** Finding a live host, the attacker runs an Nmap SYN scan to identify open ports (e.g., port 22 SSH, port 80 HTTP).
3.  **Credential Access (TA0006 - T1110.001):** Discovering port 22 (SSH) open, the attacker initiates an automated brute-force attack to guess a valid username and password combination.
4.  **Privilege Escalation (TA0004):** If a password is successfully guessed, the attacker uses the compromised SSH credentials to log in and elevate their privileges to `root` or a domain administrator.
5.  **Exfiltration (TA0010) / Impact (TA0040):** The attacker pivots to the HR database, exfiltrates payroll data, and deploys a persistent backdoor to maintain access.

---

## GRC Recommendation: Disrupting the Kill-Chain

To disrupt this kill-chain and move from a purely *detective* posture to a *preventive* stance, the following controls are recommended:

1.  **Automated Blocking (SOAR):** Integrate Suricata with a SOAR platform to trigger immediate automated firewall blocks upon detecting T1046 (SYN scan). This cuts the attack chain at the Discovery phase.
2.  **Web Application Firewall (WAF):** Deploy a WAF to protect against L7 reconnaissance (T1595.002), which the HTTP User-Agent rule (SID 9000004) is designed to detect.
3.  **Network Access Control (NAC):** Establish a policy to quarantine unknown MAC addresses or IPs attempting to scan internal hosts, severely limiting the attacker's ability to move laterally across the network.
