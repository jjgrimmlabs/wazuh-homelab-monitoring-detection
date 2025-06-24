### wazuh-homelab-monitoring-detection ###

### Purpose/Goal of this Project
**What:**  
I'm building a Wazuh-based SIEM environment to practice proactive threat detection and incident response. The lab includes:
  - 1x Debian 12 VM as the Wazuh manager
  - 1x Kali Linux VM (victim)
  - 1x Kali Linux VM (attacker)
All systems are hosted in Proxmox and live on an isolated VLAN with no internet access to simulate a controlled internal network.

### Why? ###
- Learn how to configure and monitor a SIEM
- Understand attacker behavior through safe, offline simulations
- Build real-world skills as a defender and SOC analyst

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

Then use the Wazuh Manager to:
  - Detect the attack
  - Analyze the event timeline
  - Reconfigure endpoints to close vulnerabilities
  - Re-test to confirm the threat is mitigated
  This iterative process helps me learn both offense and defense and gain insight into what sysadmins and SOC analysts must monitor and secure daily.

------------------------------------------------------------------------
### Upcoming Plans ###
  - [x] Deploy Wazuh in VLAN
  - [X] Install Wazuh agents on endpoints
  - [ ] Simulate PowerShell command
  - [ ] Document alert timeline in Wazuh
  - [ ] Harden endpoint + re-test

------------------------------------------------------------------------
### Setup Process ###
Uploaded Debian 12 .iso to Proxmox and created the VM.
Installed the OS, configured a static IP, and enabled UFW.
While an internet-connected VLAN used curl to download the Wazuh manager installer & agent installer
Installed Wazuh Manager using there curl script and erified access to the Wazuh dashboard in the browser.
Created unique agent keys for the attacker and victim VMs
Moved the Wazuh manager to the isolated VLAN.

<img width="266" alt="Proxmox Wazuh VMs" src="https://github.com/user-attachments/assets/2c1596f9-759c-437c-b519-00f14c0ad09f" />

### Attacker & Victim VM Setup ###
Created 2 Kali VMs in Proxmox and placed them in the same isolated VLAN.
Set static IP addresses, enabled UFW and allowed SSH.
Manually transferred from Wazuh Maangment VM:
- Downloaded Wazuh agent .deb installer
- Created unique client.keys file
- Copied Agent INstaller from Wazuh Manager using scp.
- Installed agents using: sudo dpkg -i wazuh-agent.deb
Edited ossec.conf with the Wazuh manager IP.
Then added the agent key to /var/ossec/etc/client.keys.
Enabled the agent with: sudo systemctl enable wazuh-agent && systemctl start wazuh-agent
Verified both agents appeared in the Wazuh dashboard.

<img width="853" alt="Wazuh Reporting Agents" src="https://github.com/user-attachments/assets/857326f3-d941-4c1a-ad2a-7bd9a516b2c1" />
