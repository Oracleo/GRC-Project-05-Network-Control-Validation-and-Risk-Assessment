# 9. MITRE ATT&CK Threat Actor Mapping

Mapping the multi-stage attack to MITRE ATT&CK allows us to visualize the adversary's full kill chain and apply precise mitigations.

## Tactical Kill-Chain Mapping
| Vulnerability / Threat | MITRE Tactic | MITRE Technique ID / Technique Name | Adversary Action Enabled |
|:---|:---|:---|:---|
| **ICMP Ping Sweep (SID 9000001)** | **TA0043 (Reconnaissance)** | **T1595.001** / Active Scanning: Port Scanning | The attacker validates the target host is alive, confirming it is a viable attack candidate. |
| **Nmap SYN Port Scan (SID 9000002)** | **TA0043 (Reconnaissance)** | **T1046** / Network Service Scanning | The attacker maps out open ports to identify potential entry points (e.g., SSH). |
| **SSH Brute Force Attempt (SID 9000003)** | **TA0006 (Credential Access)** | **T1110.001** / Brute Force: Password Guessing | The attacker automatically attempts to guess valid SSH credentials to gain Initial Access. |
| **TCP Flood / DoS (SID 9000005)** | **TA0040 (Impact)** | **T1498** / Network Denial of Service | The attacker attempts to overwhelm the network bandwidth, potentially disrupting operations. |

## Attack Chain Scenario (The "Real World" Threat)
1.  **Reconnaissance (TA0043 - T1595):** Attacker executes an ICMP ping sweep against the target network to find live hosts.
2.  **Resource Development / Scanning (TA0043 - T1046):** Finding a live host, the attacker runs an Nmap SYN scan to identify open ports.
3.  **Credential Access (TA0006 - T1110.001):** Discovering port 22 (SSH) open, the attacker initiates an automated brute-force attack to guess a valid username/password.
4.  **Privilege Escalation (TA0004):** If a password is discovered, the attacker uses the compromised SSH credentials to log in and elevate their privileges to `root`.
5.  **Exfiltration (TA0010) / Impact (TA0040):** The attacker pivots to the HR database, exfiltrates payroll data, and deploys a backdoor.

**GRC Recommendation:** To disrupt this kill-chain:
1.  Implement **Automated Blocking (SOAR)** immediately upon detection of T1046 (SYN scan), cutting the attack chain at Reconnaissance.
2.  Deploy a **Web Application Firewall (WAF)** to protect against L7 reconnaissance (detected by SID 9000004).
3.  Establish a **Network Access Control (NAC)** policy to quarantine unknown MAC/IPs attempting to scan internal hosts.
