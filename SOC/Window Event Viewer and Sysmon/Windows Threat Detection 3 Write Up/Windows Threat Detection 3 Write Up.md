Windows Threat Detection 3 Write Up 

Attacks Without Command and Control 

- When an adversary commits an RDP attack, a C2 isn’t necessary. Unless the adversary wants to regain access another time, then, they will set up a C2 immediately after the breach.  
- An advance way of maintaining C2 is by downloading an additional C2 malware that can hide in a folder (C:\TEMP) and run as a new process. This method keeps the attack going if the victim decides to delete the original attachment ([Threat Spotlight: Ransomware, trojans, and loaders - Cisco Umbrella](https://umbrella.cisco.com/blog/cybersecurity-threat-spotlight-ransomware-trojans-loaders)).  
- This is another clear descriptive case based on a APT29 Campaigns ([Tracking APT29 Phishing Campaigns | Atlassian Trello | Google Cloud Blog](https://cloud.google.com/blog/topics/threat-intelligence/tracking-apt29-phishing-campaigns)) 
Questions 
1. Which suspicious archive did the user download?  
- Downloaded file in Sysmon log will be associated with Event ID 15 ([Sysmon - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)) 
- Let access Sysmon log through Event Viewer (Should be able to navigate to it from “Windows Threat Detection 2”) or open file explore and click on the “Sysmon.evtx” 
- Also, with the File Explore, there is a downloaded file named “URGENT!.zip”. That could be the answer, however, let's make sure this is the case.  
    

	![[178.png]] 

- Filter to Event ID 15  
    

	![[179.png]]Answer: URGENT!.zip 

2. Where did the attacker hide the C2 malware file 
- This is consisting of Process Creation (Event ID 1) 
- Notice in the screenshot below, there is a PowerShell execution being implemented in the ParentCommandLine 
    

	![[180.png]] 

- This indicates that the Child CommandLine should be the path where the malicious file is hidden. 
    

	![[181.png]]Answer:  C:\Users\Administrator\AppData\Roaming\update.exe 

3. What is the domain of the Command-and-Control server?  
- To find the domain, filter to the Event ID: 22 ([Sysmon - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)) DNS Query.  
    

	![[182.png]]Answer: route.m365officesync.workers.dev 

Persistence Overview 
- The method of maintaining reliable, long-term access to a targets machine that can survive reboots and passwords changes.  
    
Persisting with RDP  
- For adversaries to maintain persistence through RDP  they can create a hidden vulnerability in the breached service utilizing backdoor or web shell.
- TA003: ([Persistence, Tactic TA0003 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/tactics/TA0003/))
	- Tactic: Persistence
		- The ability to maintain access to a system across restarts, change in credentials, and other interruption that could cut off the access. 
- T1505.003: Server Software Component: Web Shell ([Server Software Component: Web Shell, Sub-technique T1505.003 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1505/003/)). 
	- Tactic:  Persistence
		- Adversaries may install web shells on a web server to maintain persistence to a system. A web shell is a web script that is place on an open-facing web server to allow an adversary to use the web server as a gateway into the network
    
Detecting Backdoored Users 
- With adversaries creating backdoors, they can do so by creating additional accounts on the targets machine. If this the case, we can filter and monitor users account through the Event Viewer by filtering for Event ID 4720 User Account Creation ([Appendix L - Events to Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor))  
- Another method is that an adversary can create account to a privileged group, and this can be filter through Event ID 4732 Security Enabled Local Group ([Appendix L - Events to Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor)) 
    
Resetting Passwords 
- If there’s an account on the target system that hasn’t been used or forgotten about, they may be motivated to change the password of that user.  
- This can be filter through Event ID 4724 Reset Account Password ([Appendix L - Events to Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor)) 
    

Questions 

1. How many times did the threat actor fail to log into the Administrator?  
- We first must go to security saved logs.  
    

	![[183.png]] 

- Revisiting “Windows Threat Detection 1”, we can filter this action with Event ID 4625 
    

	![[184.png]]Answer: 6 

2. After Successful login, which backdoor user did the attacker create?  
- Let filter to Event ID: 4720 Creating User 
    

	![[185.png]]Answer: support 

3. Which privileged group was the backdoor user added to?  
- Let's filter to Event ID 4732 Privilege Group  
    

	![[186.png]]Answer: Administrators 

Malware Persistence 
- Maintaining persistence with malware requires an actively running malware that maintains a connection with their C2 server even after a system reboot.  
    

|   |   |   |
|---|---|---|
|Persistence Method|Attack Example|Event ID Logging|
|Create a Windows Service (Runs after OS startup)|sc create "BadService" binpath= "C:\malware.exe" start= auto|Launch of sc.exe: Sysmon / 1 Service creation: Security / 4697|
|Create a Scheduled Task (Run after OS startup)|schtasks /create /tn "BadTask" /tr "C:\malware.exe" /sc onstart /ru System|Launch of schtasks.exe: Sysmon / 1 Scheduled task creation: Security / 4698|

Questions 

1. Which Windows service was created to persist in the Nessie malware?  
- Looking at the chart above, we can use Event ID 4697 Service Creation 
    

	![[187.png]]Answer: Data Protection Service 

2. Which Scheduled task was created to persist in the Troy malware?  
- Let filter to Event ID 4698 Schedule Task Creation 
- Below, we can see the command for troy.exe 
    

	![[188.png]]
	![[189.png]]
	Answer: AmazonSync 

3. What flag do you get after finding and running the Troy malware?  
- This involves several steps. 
- First, Let go to the Sysmon log and filter to Event ID 1 Process Creation.  
- We want to find the Process Creation with troy.exe malware. You can filter CRTL+F troy 
- Second, once we find the Process, copy the Parent Command Line – Shown Below:  
    

	![[190.png]] 

- Third, after copying the parent command line, Open File Explore and navigate to the Troy File (Local Disk > Programs > Common Files > troy.exe). Double-click on the troy.exe.  
- Fourth, a Command Prompt will open, copy and past the Parent Command Line to generate the flag.  
    

	![[191.png]]Answer: THM{c2_is_on_schedule!} 

Run Keys and Startup 
- Windows provides a few per-user persistence methods that are actively used by both legitimate tools and malware. 
    

|   |   |   |
|---|---|---|
|Persistence Method|Attack Example|Event ID Logging|
|Add malware to Startup Folder (Runs upon user login)|copy C:\malware.exe "%AppData%\Microsoft\Windows\Start Menu\Programs\Startup\malware.exe"|New startup item: Sysmon Event ID 11|
|Add malware to "Run" keys (Runs upon user login)|reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v BadKey /t REG_SZ /d "C:\malware.exe"|New registry value: Sysmon Event ID 13|

Questions 

1. What is the parent process image of the “Odin” malware?  
    

	![[192.png]] 

- Let open task 5 Sysmon log and filter to Event ID 11 and search for the Odin malware to find the parent process.  
    

	![[193.png]] 

- We can see the path above to the odin.cmd. This indicates that the file was downloaded after the Process Creations in the User Administrator Directory. 
- Let filter to Event ID 1 Process Creation 
    

	![[194.png]]Answer: C:\Windows\explorer.exe 

- Here, we see the same User Administrator with the full Child CommandLine showing there was a download executed through chrome browser.  
- Both Event ID 11 and Event ID 1 is associated with the same User  
- Also look at the time frames. After Event ID 11 was executed, it immediately created a process moments after.  

2. What is the last line that the “Odin” malware outputs?  
- Let's open the Sysmon.evtx after the execution file 
	![[195.png]] 
- Filter to Event ID 1 Process Creation 
- We want to look for any suspicious commandline associated with the User “Administrator” 
    

	![[196.png]]Answer: Done doing bad stuff! 

3. What flag do you get after finding and running the Kitten malware 
- This will be in Event ID 1 Process Creation.  
- Look for a Process with “Kitten” 
    

	![[197.png]] 

- Next copy the command line then navigate to the executable file in File Explore (Users>Public>kitten.exe) click on kitten.exe 
    

	![[198.png]] 

- It is asking for the name of the run key. Let filter Event 13 New Registry in the “Before execution Sysmon.evtx”.  
    

	![[199.png]] 

	![[200.png]] 

Answer: THM{persisting_in_basket!}