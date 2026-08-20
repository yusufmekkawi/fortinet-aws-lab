# FortiGate NSE4 Hands-On Lab (AWS)

A self-built FortiGate NSE4 lab hosted on AWS, covering identity authentication, 
IPsec VPN, SSL inspection, and a full UTM security stack — built to validate 
NSE4 exam domains hands-on rather than just through theory.

## Topology

![Lab Topology](diagrams/topology.png)

- FortiGate-VM (PAYG, FortiOS 7.6.7) — dual-homed across a public and private subnet
- Windows Server 2025 — Domain Controller + LDAP + Certificate Services
- Windows Client — private subnet, no direct internet access
- Remote access via IPsec Dialup VPN (FortiClient)

## What's covered

- **Identity (LDAP)** — AD-integrated authentication; diagnosed and resolved a 
  Server 2025 LDAP signing/channel-binding rejection via event log analysis
- **Remote Access VPN (IPsec Dialup)** — migrated from SSL-VPN, which Fortinet 
  removed in FortiOS 7.6.3; resolved IKE proposal mismatches across multiple 
  client devices and a port-443 conflict with the admin GUI
- **SSL/TLS Deep Inspection** — full CA trust chain working end-to-end with 
  a self-signed enterprise CA
- **Web Filtering** — category-based and quota-based blocking, tested live
- **AntiVirus** — EICAR test file detected and quarantined
- **Application Control** — differentiated policy across multiple apps 
  (block, quarantine, pass)
- **Intrusion Prevention** — live SQL injection signature triggered and dropped
- **DoS Protection** — genuine ICMP flood detected and mitigated (46 sessions, 
  Critical severity)

## Key lessons (the parts that mattered more than the config steps)

1. **SSL-VPN is gone.** FortiOS 7.6.3 removed SSL-VPN tunnel mode entirely — 
   IPsec Dialup is the supported path forward, and it's not a drop-in swap.
2. **"Strong(er) authentication required" isn't always what it looks like.** 
   Traced through Event IDs 2889 → 1535 to the actual LDAP signing requirement, 
   rather than guessing at GPO settings.
3. **Windows `ping -f` doesn't mean flood mode** — it sets the Don't Fragment 
   bit. Real DoS testing needs a proper flood tool.
4. **IKE proposals aren't universal** — a working tunnel on one client can 
   fail on another due to differing default cipher/DH offers.

## Screenshots

See `/screenshots` for evidence of each domain above.

## Notes

This is a lab environment — PSKs, certificates, and configs are for demonstration 
only and not representative of production hardening practices.
