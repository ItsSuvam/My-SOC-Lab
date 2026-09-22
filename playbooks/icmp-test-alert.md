# Playbook — Custom ICMP Test Alert (sid:9000001)

## Purpose
Validate the full detection pipeline end-to-end: Suricata -> Wazuh agent -> Wazuh manager -> Dashboard.

## Detection
- Rule: alert icmp any any -> any any (msg:"Test ICMP Alert - Suvam Lab"; sid:9000001; rev:1;)
- Source: Suricata IDS on the Linux endpoint (eve.json forwarded to the Wazuh agent)
- Wazuh assigned rule.id: 86601

## Investigation Steps
1. Confirm the alert in the Wazuh dashboard (Threat Hunting -> Events), filter rule.id:86601.
2. Check src_ip / dest_ip in the alert to confirm it matches expected test traffic.
3. Cross-check agent.name to confirm the source endpoint.

## Escalation Criteria
This is a validation rule, not a real threat signature - not escalated in production use.
In a real detection, escalate if src_ip is external/unexpected or if alert volume spikes abnormally.

## Notes
Used to confirm the lab pipeline works end-to-end before building MITRE ATT&CK-mapped detections.
