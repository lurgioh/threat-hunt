# Hunt 11: Just Another Day — Compromised Account

## Platform

* Log N Pacific Cyber Range
* Microsoft Sentinel
* Microsoft Defender Advanced Hunting (KQL)

## Scope

**Hosts:** `nh-*`
**Date Range:** March 8–18, 2026

## Overview

A billing analyst account (`j.morris`) was flagged for suspicious activity. While the initial assumption was that the account belonged to a former employee whose access had not been revoked, the investigation showed something different.

The account was being used remotely over RDP from external IP addresses, followed by reconnaissance, lateral movement, file tampering, and unauthorized access to sensitive HR data. No malware or exploits were observed. The attacker relied entirely on legitimate credentials and built-in Windows utilities, making this a classic living-off-the-land (LOTL) intrusion.

---

## Key Findings

### Initial Access

* Multiple `RemoteInteractive` (RDP) logons were observed from external IP addresses.
* Example source IP:

  * `193.36.225.245`
* The activity was inconsistent with the user's normal login behavior.

### Reconnaissance

The attacker executed several native Windows commands to gather information about the environment, including:

* `whoami`
* `hostname`
* `net use`
* `net view`
* `net view /domain`
* `arp`
* `nslookup`

No custom tooling or malware was used during reconnaissance.

### Lateral Movement

The attacker used RDP to pivot through the environment.

Observed destinations:

* IT workstation (no significant follow-on activity)
* File server (primary objective)

### File Tampering

The attacker modified billing records by:

* Editing an existing file in the **Billing Approved** folder
* Creating a new file and renaming it to match the approved invoice naming convention
* Modifying the billing audit trail log

These actions suggest an attempt to blend malicious changes into legitimate business operations.

### Data Collection

The attacker accessed sensitive HR documents across multiple employees, including:

* Payroll records
* Employee awards
* Onboarding documentation
* Payroll review files

The breadth of accessed data indicates broad collection rather than targeting a single individual.

---

## Root Cause

The investigation determined that the incident resulted from compromised user credentials.

The attacker authenticated successfully using the billing analyst account and performed all activity with legitimate Windows utilities, including:

* `cmd`
* `net`
* `arp`
* `nslookup`
* `mstsc`
* `whoami`

No malware or exploitation activity was identified.

---

## Detection Opportunities

Organizations can improve detection of similar attacks by:

* Monitoring `RemoteInteractive` (RDP) logons originating from external or non-corporate IP addresses.
* Baselining normal file-share access by department and alerting when users access data outside their expected role.
* Detecting files that are created and immediately renamed to match existing naming conventions in sensitive directories.
* Alerting when audit logs or financial approval records are modified outside of normal business workflows.
* Correlating RDP activity with native administrative command execution (`whoami`, `net`, `arp`, `nslookup`) to identify potential hands-on-keyboard activity.

---

## MITRE ATT&CK Techniques

| Tactic           | Technique                                      |
| ---------------- | ---------------------------------------------- |
| Initial Access   | Valid Accounts (T1078)                         |
| Discovery        | System Owner/User Discovery (T1033)            |
| Discovery        | System Information Discovery (T1082)           |
| Discovery        | Network Service Discovery (T1046)              |
| Discovery        | Network Share Discovery (T1135)                |
| Lateral Movement | Remote Services: RDP (T1021.001)               |
| Collection       | Data from Network Shared Drive (T1039)         |
| Defense Evasion  | Living Off the Land (Native Windows Utilities) |

---

## Skills Demonstrated

* Microsoft Sentinel investigation
* Microsoft Defender Advanced Hunting (KQL)
* Authentication analysis
* RDP log analysis
* Lateral movement investigation
* File system forensics
* Living-off-the-land detection
* MITRE ATT&CK mapping
* Incident documentation
