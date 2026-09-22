# My SOC Lab — Wazuh + Suricata (WSL2)

A self-built SOC lab for hands-on practice in detection engineering, log analysis,
and MITRE ATT&CK-mapped alerting. Built entirely on WSL2 - no dedicated VM host required.

## Architecture
- Wazuh manager, indexer, dashboard - Kali Linux (WSL2)
- Wazuh agent + Suricata IDS - Ubuntu 22.04 (WSL2)
- Suricata eve.json alerts are forwarded into Wazuh via a localfile JSON integration,
  so Suricata alerts appear correlated in the Wazuh dashboard (see config/).

## What's done
- Deployed Wazuh central components (manager, indexer, dashboard) and a monitored Linux endpoint
- Installed and tuned Suricata IDS on the endpoint (corrected the capture interface,
  resolved a rule-file inclusion issue so custom rules actually load - see notes below)
- Wrote a custom Suricata detection rule (sid:9000001) and validated it end-to-end:
  Suricata -> Wazuh agent -> Wazuh manager -> Dashboard (Wazuh assigned rule.id: 86601,
  1000+ hits confirmed over 24h - see evidence/)
- Wrote the first investigation playbook (playbooks/icmp-test-alert.md)

## In progress
- Windows endpoint with Sysmon
- MITRE ATT&CK-mapped custom rules (SSH brute force - T1110, suspicious PowerShell - T1059.001,
  new local user creation - T1136.001), each validated with Atomic Red Team
- Wazuh Active Response (automated containment action)
- VirusTotal integration for alert enrichment
- False-positive tuning with before/after evidence

## Repo structure
- rules/      custom detection rules (Suricata, Wazuh local_rules.xml as they're added)
- playbooks/  per-use-case investigation and escalation steps
- evidence/   dashboard screenshots proving each detection fired
- config/     relevant config snippets (Wazuh-Suricata integration, etc.)

## Troubleshooting notes (kept for anyone doing this on WSL2)
- WSL2 mirrored networking mode was required for https://localhost to reach the
  Wazuh dashboard directly from Windows.
- Suricata defaulted to listening on eth0, which is down in this WSL setup - the actual
  active interface was eth2 (checked with `ip a`). Fixed in suricata.yaml under af-packet.
- A custom rule in local.rules silently failed to load until it was explicitly added to
  the rule-files list in suricata.yaml (Suricata doesn't auto-include it).
