### ### SSH Brute Force ### ####
Thanks for checking out my ongoing Wazuh SIEM penetration and detection project. If you have suggestions, feedback, or tips feel free to connect with me on LinkedIn. I’m open to learning and improving. For my first simulated attack, I ran a basic SSH brute force attempt on my victim VM to highlight how important it is to harden SSH and use strong credentials.

### Setup ###
To simulate poor security practices, I misconfigured the victim VM (Kali Linux) by editing PermitRootLogin using nano /etc/ssh/sshd_config file and enabling PermitRootLogin to yes. Then I verified SSH was running by checking the service status: systemctl status ssh. Once confirmed, I logged out of the victim machine and switched to my attacker VM.

Before launching the attack, I verified connectivity with: ping <victim_ip>.
Then I created a custom password list using: echo -e "abcd\npassword\n12345\n7890" > /home/attacker/pwrdlist.txt (Each password is on a new line using \n).

### Attack ###
With the list created, I ran Hydra to brute force SSH: hydra -l root -P /home/attacker/pwrdlist.txt ssh://<victim_ip> Hydra attempted each password until it finds the correct one (if there is one from the list). This confirms the risk of weak credentials, especially with root login enabled.

<img width="756" alt="Screenshot 2025-07-06 at 8 31 50 PM" src="https://github.com/user-attachments/assets/2e8537b6-c0bd-4364-9871-6b8f91b4e2a0" />

### SIEM Detection Review ###
After the attack, I logged into the Wazuh dashboard to verify if the activity was detected. Wazuh correctly flagged/identified:

### Multiple failed SSH attempts ###
A successful login from the attacker IP
Login to the root account from attacker IP

<img width="758" alt="Screenshot 2025-07-06 at 8 32 35 PM" src="https://github.com/user-attachments/assets/34864149-0896-4555-999c-1e6b95d581e4" />

### Cleanup/Revert ###
To close the SSH vulnerability:

I disabled PermitRootLogin login using nano /etc/ssh/sshd_config
Restarted SSH to apply changes sudo systemctl restart ssh

### Lessons Learned ###
Default configurations can be dangerous if not changed.
SSH brute force attacks are still common and easily automated.
Wazuh effectively detects brute force patterns and successful logins.
This basic simulation showcases why it's critical to:

Disable password-based root SSH access
Use key-based auth
Harden credentials
Monitor and alert on failed login attempts
