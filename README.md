Hunt 11: Just Another Day — Compromised Account
Platform: Log N Pacific Cyber Range | Tools: Microsoft Sentinel / Defender Advanced Hunting (KQL) | Scope: nh-* hosts, 08–18 March 2026
Summary
A billing analyst account (j.morris) was flagged for unusual activity. The obvious explanation — an insider who forgot to have access revoked — didn't hold up. Logs showed the account being driven remotely over RDP from external IP addresses, running native recon commands, pivoting across the network, tampering with billing approval/audit records, and pulling sensitive HR files belonging to multiple employees. No malware, no exploits — just a valid account being puppeted by an outside actor using living-off-the-land tools.
Key Findings

Initial access: Repeated RemoteInteractive (RDP) logons to the billing workstation from external IPs (e.g. 193.36.225.245), far outside the account's normal behavior.
Recon: whoami, hostname, net use, net view, net view /domain, ARP sweep, nslookup — all native tools, no custom malware.
Lateral movement: RDP pivots to an IT workstation (dead end, no activity) and the file server (where the real damage happened).
Tampering: Modified a file already in the Billing Approved folder, created/renamed a decoy file to match approved-invoice naming, and edited the audit trail log.
Data collection: Touched HR payroll, awards, and onboarding files across multiple employees — plus copied a different employee's payroll review after pivoting to the file server. Breadth across categories/people points to indiscriminate collection, not a single targeted grab.

Root Cause
Compromised credentials, used remotely by an external actor. No malware involved — entirely native OS tools (cmd, net, arp, nslookup, mstsc, whoami).
Detection Takeaways

Alert on RemoteInteractive logons to workstations from non-corporate/external IPs.
Baseline file-share access per role — a billing analyst writing to HR Payroll should stand out.
Watch for files created and immediately renamed to blend into a sensitive folder's naming convention.
