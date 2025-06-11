### wazuh-homelab-monitoring-detection ###

### Purpose/Goal of this Project
**What:**  
I'm building a Wazuh-based SIEM environment to practice proactive threat detection and incident response. The lab includes:
  - 1x Debian 12 VM as the Wazuh manager
  - 1x Windows 11 VM (endpoint target)
  - 1x Kali Linux VM (attacker)
All systems are hosted in Proxmox and live on an isolated VLAN with no internet access to simulate a controlled internal network.

### Why? ###
To strengthen my cybersecurity defender skills while also learning how attackers think and operate. This hands-on lab helps me understand detection, misconfiguration risks, and how to harden systems in real-world environments.

### Learning Objectives ###
  - Set up and configure Wazuh SIEM
  - Practice alert triage and event correlation
  - Simulate basic attack behavior (PowerShell, failed logins)
  - Understand endpoint misconfigurations and hardening
  - Learn by doing, documenting, and refining
  
------------------------------------------------------------------------

### How ###
I’ll simulate attacks from the Kali Linux VM targeting the Windows machine using:
  - PowerShell payloads (offline)
  - Exploiting open ports or vulnerable services
  - Obfuscation
  - Privilege escalation

Then use Wazuh to:
  - Detect the attack
  - Analyze the event timeline
  - Reconfigure endpoints to close vulnerabilities
  - Re-test to confirm the threat is mitigated
  This iterative process helps me learn both offense and defense and gain insight into what sysadmins and SOC analysts must monitor and secure daily.

------------------------------------------------------------------------
### Upcoming Plans ###
  - [x] Deploy Wazuh in VLAN
  - [ ] Install Wazuh agents on endpoints
  - [ ] Simulate PowerShell command
  - [ ] Document alert timeline in Wazuh
  - [ ] Harden endpoint + re-test
