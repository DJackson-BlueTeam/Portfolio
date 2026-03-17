Windows Threat Detection 1 Write Up
---

Initial Access
---

- Corporate websites require open http port to show content; a mail server needs an active SMTP (Simple Mail Transfer Protocol) port open to handle emails, and IT departments need RDP (Remote Desktop Protocol) to manage machines remotely.  
- Even though this is necessary for IT departments to conduct business efficiently, adversaries may use bots to detect open ports, weak passwords, and even unpatched vulnerabilities.    
- _T1113: Screen Capture_ ([External Remote Services, Technique T1133 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1133/)) 
	- Tactic: Collection
		- Adversaries may attempt to take screenshot of the desktop to gather information. Screen capturing can reveal visual data such as open windows, user activity, and sensitive information displayed on the screen. This is often achieved using specialized malware to capture the content of the display or specific application windows. 
- _T1190: Exploiting Public Facing Application_ ([Exploit Public-Facing Application, Technique T1190 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1190/)) :  
	- Tactic: Initial Access
		- Adversaries may exploit a software vulnerability in an internet facing host or application to gain initial access to a network. This involve targeting weaknesses in a web server, databases, or other services that are directly reachable from the internet.
    



User Drive Method
---

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/139.png)
 
- The diagram above shows how social engineering helps adversaries persist in networks.  
- This can happen by infected USB or by a phishing email (which are most common in corporate network exploitable)
- _T1566: Phishing_ ([Phishing, Technique T1566 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1566/))
	- Tactic: Initial Access 
		- Adversaries may send phishing messages to gain access to a target system. Phishing is a social engineering strategy that an adversary will use to deceive users into performing actions that are unknowingly malicious.  
- _T1091: Replication Through Removable Media_ ([Replication Through Removable Media, Technique T1091 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1091/)) 
- Tactic: Initial Access, Later Movement 
	- Adversaries moves to other systems or gain initial access by coping malware to removable media and waiting for the media to be inserted into a target system. This technique bypass network-based security controls and can be used to bridge air-gapped systems or spread within a network when a user connects the infected device to a computer.    

Initial Access: RDP 
---
- The chart below explains the steps of an RDP breach (Ransomware Deployment Protocol): 
    

|   |   |   |
|---|---|---|
|**#**|**Step of Attack**|**Detection Opportunity**|
|1|Network Scan Botnet scans our IP and detects an exposed RDP port|N/A. Network attacks are out of the room scope|
|2|RDP Brute Force Botnet starts a brute force of common user names (Administrator, admin, support, etc.)|1. Open Security logs and filter for the failed logins (event ID 4625) 2. Filter for logon types 3 and 10, meaning remote logons 3. Filter for logins from external IPs (use "Source IP" field) 4. That's it. You have detected a potential RDP brute force|
|3|Initial Access via RDP After around 100 attempts, the botnet guesses the correct password and enters the system|1. Continue with the list from the previous step 2. Switch the event ID filter to 4624 (successful logins) 3. Check the account under which the logon was made 4. Now you know which account was used for the Initial Access|
|4|Further Malicious Actions Two hours after the breach, the threat actor logs in via RDP and reviews the Desktop|1. Continue with the list from the previous step 2. Filter for logon type 10, indicating interactive RDP login 3. Copy the "Logon ID" field from the logon event 4. Open Sysmon logs and search events with the same "Logon ID" 5. You will see all processes started by the threat actor via RDP|

**Logging Brute Force**  

- It's not difficult to spot a brute force in the Windows Event Logs using Event ID 4625  
    

**Questions**

**1. Which user seems to be most actively brute-forced by botnets?**  
- Let's open Event View and filter to Event ID 4625 (failed logins) 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/140.png) 

Answer: Administrator 

**2. Which IP managed to breach the host via RDP (Logon Type 10)?**  
- Let's filter to Event ID: 4624 to determine the successful logon  
- Look for the user “Administrator” that has successfully login and the IP will be associated with the breach.  

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/141.png)

Answer: 203[.]205[.]34[.]107 

**3. What is the real workstation name of the adversary?**  
- The Real WorkStation Name is associated with Type 3 Logon (Network) with the Event ID 4624 under the user “Administrator” 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/142.png) 

Answer: DESKTOP-QNBC4UU 

Initial Access: Phishing
---
- Phishing attacks are a major threat of sense associated with social engineering 
- _T1566: Phishing_ ([Phishing, Technique T1566 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1566/))
	- Tactic: Initial Access
		- Adversaries may send phishing messages to gain access to victims systems. 

Binary Attachments
---
- In windows there are many executable extensions that many users are unaware about ([Security-Blocked-File-Extensions-Attachments/list-of-blocked-file-extensions.txt at main · michalzobec/Security-Blocked-File-Extensions-Attachments](https://github.com/michalzobec/Security-Blocked-File-Extensions-Attachments/blob/main/list-of-blocked-file-extensions.txt)).  
    
- Windows also hide known file extensions by default and may be displayed as a normal file. Example, “program.exe” may be shown as “program”. 
- Most adversaries would abuse windows default by naming a virus “invoice.pdf.exe” and change the icon to “invoice.pdf”. This is in the MITRE ATT&CK Framework 
- _T1036.007: Masquerading: Double File Extension_ ([Masquerading: Double File Extension, Sub-technique T1036.007 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1036/007/))
	- Tactic: Defense Evasion
		- When a adversary abuse double file extension to hide the true file type of a malicious file. By naming a malicious file with multiple extensions, the adversary can trick users into believing the file is a benign document or media file. 
		- Benign Document: is a safe harmless file that contain no malicious code.  
    

**LNK Attachment** 

- To avoid AV (Anti-Virus Detection), adversaries may prefer using a PowerShell script instead of binaries.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/143.png) 

**Questions** 

**1. Run the www[.]skype[.]com file from the Phishing Case 1 folder, which flag do you get?**  
- This is a simple example of a malicious script that runs. 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/144.png)

Answer: THM{misleading_extension} 

**2. From Phishing Case 2, which URL does the malicious LNK download the next stage of malware?**  
- Unzip the fold then right-click on the “Official Website” then view properties. You will find the link.  
	![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/145.png)

	Answer:  hxxp://wp16[.]hqywlqpa[.]thm:8000/cgi-bin/f 

**3. From the Phishing Case 3 folder, what is the name of the double-extension file?** 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/146.png)

Answer: best-cat[.]jpg[.]exe 
	
**4. Which file did the user download via the web browser?**  
- Downloaded file in Sysmon logs is associated with Event ID 15. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/147.png) 

Answer: C:\Users\Administrator\Downloads\top-cats.zip 

**5. In which folder did the user unarchive the suspicious file?**  
- In the same Event ID, we can find the folder.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/148.png) 

Answer: C:\Users\Administrator\Pictures 

**6. What is the process ID of the launched phishing malware?**  
- Launching the malware corelates with the DNS query of the site the malware was launched from, so we will use the Event ID 22.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/149.png) 

Answer: 5484 

**7. Which malicious domain did the malware try to connect to?**  
- The answer is in the screenshot above.  
Answer: rjj.store 

**8. Which USB file was launched by the user?**  
- Let open the USB Sysmon.evtx and review the events. 
- We will filter to Event ID 13 since this is a registry event that is occurring.
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/150.png)

Answer: E:\Open Sandisk 4GB USB.exe
**9. Which suspicious file did the malware drop to the disk?**
	- The answer will be in the same registry event as the launch was initiated
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/151%201.png)

Answer: C:\Users\Public\Documents\winupdate.exe

**10. To which other USB did the malware propagate?** 
	- For a USB to propagate to another USB type, it creates a file within the system.
	- We must filter to Event ID 11 (File Creation)
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ca822390450bfe9849a8bb26008200547fc4ca1a/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%201/Windows%20Threat%20Detection%201%20Img/152.png)
	
Answer: F:
	
