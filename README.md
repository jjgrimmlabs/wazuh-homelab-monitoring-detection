### wazuh-homelab-monitoring-detection ###

### Purpose/Goal of this Project
**What:**  
I'm building a Wazuh-based SIEM environment to practice proactive threat detection and incident response. The lab includes:
  - 1x Debian 12 VM as the Wazuh manager
  - 1x Kali Linux VM (victim)
  - 1x Kali Linux VM (attacker)
All systems are hosted in Proxmox and live on an isolated VLAN with no internet access to simulate a controlled internal network.

<img width="268" alt="Screenshot 2025-06-23 at 9 46 03 PM" src="https://github.com/user-attachments/assets/975fca63-61c4-488d-9464-434a59242698" />

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
I’ll simulate attacks from the Kali Linux VM targeting another Kali Linux VM using:
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

------------------------------------------------------------------------
------------------------------------------------------------------------
###  ### SSH Brute Force ### ### 
Thanks for checking out my ongoing Wazuh SIEM penetration and detection project. If you have suggestions, feedback, or tips feel free to connect with me on LinkedIn. I’m open to learning and improving. For my first simulated attack, I ran a basic SSH brute force attempt on my victim VM to highlight how important it is to harden SSH and use strong credentials.

### Setup ### 
To simulate poor security practices, I misconfigured the victim VM (Kali Linux) by editing PermitRootLogin using nano /etc/ssh/sshd_config file and enabling PermitRootLogin to yes. Then I verified SSH was running by checking the service status: systemctl status ssh. Once confirmed, I logged out of the victim machine and switched to my attacker VM.

- Before launching the attack, I verified connectivity with: ping <victim_ip>.
- Then I created a custom password list using: echo -e "abcd\npassword\n12345\n7890" > /home/attacker/pwrdlist.txt (Each password is on a new line using \n).

### Attack ###
With the list created, I ran Hydra to brute force SSH: hydra -l root -P /home/attacker/pwrdlist.txt ssh://<victim_ip>
Hydra attempted each password until it finds the correct one (if there is one from the list). This confirms the risk of weak credentials, especially with root login enabled.

<img width="1215" alt="Screenshot 2025-06-26 at 10 07 21 PM" src="https://github.com/user-attachments/assets/ae9abd0a-73cc-4ecb-aa9d-80f10ae993f4" />

### SIEM Detection Review ### 
After the attack, I logged into the Wazuh dashboard to verify if the activity was detected.
Wazuh correctly flagged/identified:
- Multiple failed SSH attempts
- A successful login from the attacker IP
- Login to the root account from attacker IP

<img width="1051" alt="Screenshot 2025-06-26 at 10 07 42 PM" src="https://github.com/user-attachments/assets/30aa2eaf-ef06-418e-a2ca-6a50c24dedfd" />

### Revert section ### 
To close the SSH vulnerability:
- I disabled PermitRootLogin login using nano /etc/ssh/sshd_config
- Restarted SSH to apply changes sudo systemctl restart ssh


### Lessons Learned ### 
- Default configurations can be dangerous if not changed.
- SSH brute force attacks are still common and easily automated.
- Wazuh effectively detects brute force patterns and successful logins.

This basic simulation showcases why it's critical to:
- Disable password-based root SSH access
- Use key-based auth
- Harden credentials
- Monitor and alert on failed login attempts
