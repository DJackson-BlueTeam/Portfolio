Windows Event Log Intro Write-Up 
---

Every file that is creates, logging in or out, creating users, permissions etc, those events are logged in Windows Event Logs 

1. Log Source: Every event text file is related to a single item on the left panel 
2. Log List: Each row in the log list contains you can view by: 
	- Keywords: some events indicate is the action was successful or not
	- Date and Time: The timestamp when the event occurred. 
	- Event ID (we will discuss later): Is a unique number identifier for action of an event. 
3. Log Details: This is the details of the event that has occurred within the logs in plain text or in "XML" format 
4. Filters Menu: Used for filtering logs.  

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/114.png) 
    

|   |   |   |   |
|---|---|---|---|
|Event ID|Purpose|Logging|Limitations|
|4624 (Successful Logon)|Detect suspicious RDP/network logins and identify the attack starting point|Logged on the target machine, the one you are trying to access|Noisy. You will see hundreds of logon events per minute on loaded servers|
|4625 (Failed Logon)|Detect brute force, password spraying, or vulnerability scanning|Logged on the target machine, the one you are trying to access|Inconsistent. The logs have lots of caveats that may trick you into the wrong understanding of the event|

Structure of 4624/4625 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/115.png) 

Detecting RDP (Remote Desktop Protocol) Brute Force
---

RDP: Remote Desktop Protocol is a Microsoft technical standard that allows you to connect and control a computer from a remote location over the network.  

- When looking for RDP brute force, we want to keep an eye on logon type 3 (Network)) and type 10 (RDP logins) - this is only for misconfigured systems that is still using the older versions of Microsoft.  
- The main RED FLAGS to look for are listed below: 
1. Many attempts on users like admin, helpdesk and CCTV (Close Circuit Television: is a system where a video camera transmits signals to a specific limited set of monitors rather it being public.) 
2. Many login failures (Event Id: 4625) on a signal account, usually Administrators  
3. Workstations Name does not match corporate patterns (ex: Linux) 
4. Source IP is not expected 
    

Analyze RDP logons
---

1. Search for Security logon with the filter 4624 (successful logon) 
2. Look for event with logon type 10 (RDP logins) 
3. The RED FLAGS are either a preceding brute force or a suspicious source IP/host name 
4. If the login was malicious, determine what happened next:  
5. Windows assigns a logon ID for every successful login 
6. Logon ID is a unique session identifier.  

**Questions** 

**1. Which IP performed a brute force of the THM-PC?** 
- To determine which IP conducted a brute force attack, we need to filter to the Event ID: 4625 (failed logon attempts) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/116.png)

- Once we filter the Event, we will see multiple failed logon attempts associated with the Event ID: 4625 
- To observe what IP conducted the brute force attempt we will go to the detail tab of any of the Event ID: 4625 to observe the output 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/117.png) 

Answer: 10.10.53.248 

**2. Which user has been breached because of the attack?** 
- If the user was breached that indicates that the adversary successfully logon to the account (Event ID: 4624)
- We will need to filter the logon ID 4624 and observe the output with the IP associated with the brute force attack.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/118.png)

Answer: Administrator 

**3. What is the logon ID of the malicious RDP (Type 10) login?** 
- In the screenshot above we can see “TargetLogonID” 
Answer: 0x183C36D 

Security Log: User Management
---
- Below are some additional Event IDs associated with user management logs.  
- This allows an analyst to determine which user is conducting malicious attacks.  

|   |   |   |
|---|---|---|
|Event ID|Description|Malicious Usage|
|4720 / 4722 / 4738|A user account was created / enabled / changed|Attackers might create a backdoor account or even enable an old one to avoid detection|
|4725 / 4726|A user account was disabled / deleted|In some advanced cases, threat actors may disable privileged SOC accounts to slow down their actions|
|4723 / 4724|A user changed their password / User's password was reset|Given enough permissions, threat actors might reset the password and then access the required user|
|4732 / 4733|A user was added to / removed from a security group|Attackers often add their backdoor accounts to privileged groups like "[Administrators](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups#administrators)"|

- All user management event has similar structure and can be split into 3 parts,  

1. Subject: The account doing the actions  
2. Object: Can be named different depending on an event ID 
3. Details: target group for 4732/4733 events, or new users like full name or passwords expirations.  
    
Hunt for Backdoored User
---

1. Filter to logs 4720 (user account created)/4723(user password changed) 
2. RED FLAGS to look for when review the events: 
3. No one from the IT department can confirm the action. 
4. Changes made during non-working hours or on weekends  
5. The “Subject” username is unknown or unexpected. 
6. Target user’s name does not follow a usual naming pattern 
7. If you confirmed that the action is malicious, search for the login details 
8. Copy the logon ID field from your 4720/4732 event
9. Find similar login events with the same logon id 
10. Refer to workbooks  
    
Questions  

**1. Which user was created by the attacker soon after the RDP login?** 
- We will use the Event ID 4720 to determine what user was created  
- This should still be associated with the administrator account that was breached. 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/119.png) 

Answer: svc_sysrestore 

**2. Which two privileged groups were the backdoor user added to?** 
- To identify the privilege that was added to the breached user, we will use the Event ID: 4732 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/120.png)

- Review the “TargetUserName”  that is associated with the “SubjectUserName” 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/121.png)

Answer: Backup Operators, Remote Desktop Users 
- By groups are associated with the "Administrator” user that was breached  
    
**3. Does the Logon ID field match what you saw in the previous task?**  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/122.png)

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/123.png)

Answer: Yes 

Sysmon: Process Monitoring 
---
- Sysmon is a Microsoft Sysinternals that is equipped with advanced monitoring in addition to the default system logs.  
    

|   |   |   |
|---|---|---|
|Event Code|Purpose|Limitations|
|4688 (Security Log: Process Creation)|Log an event every time a new process is launched, including its command line and parent process details|Disabled by default, you need to enable it by following the [official documentation](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/component-updates/command-line-process-auditing)|
|1 (Sysmon: Process Creation)|Replace 4688 event code and provide more advanced fields like process hash and its signature|Sysmon is an external tool not installed by default. Check out the [Sysmon official page](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)|

**Sysmon Event ID 1 in Action:** 

1. Process Info: Context of launched process, including its PID (Process ID: unique identifier assigned to each running process in a system) and command line 
2. Parent Info: Information regarding the parent process, useful to build a process tree or an attack chain 
3. Binary Info: Process hash, signature, and PE metadata - file format for executables, object code, DLL. Encapsulates the information necessary for the Windows OS to manage the wrapped executable code. 
4. User Context: User running the process and Logon ID, which is the same as in the security logs.  

**Analyze Process:**
1. Open Sysmon logs and filter for event ID 1 
2. Review the fields from the process and binary info groups. RED FLAGS 
3. Image is an uncommon directory “C:\Temp or C:|Users|Public 
4. Process is suspiciously named “aa.exe or jqyvpqldou.exe” 
5. Process hash (MD5 or SHA256) matches as malware on [VirusTotal - Home](https://www.virustotal.com/gui/home/search) 
6. Review the fields from the parent process group. RED FLAGS 
7. Parent matches red flags from step 2 (suspicious name, path, or hash) 
8. Parent is not expected (Notepad launching some CMD command) 
9. If still in doubt, go up the process tree until you are confident in your verdict: 
10. Find the preceding event where ProcessID equal ParentProcessID in your event 
11. Analyze it by following steps 2 and 3 (suspicious parent, name, path or hash) 
12. Trace the attack chain by filtering all Security and Sysmon events with the same Logon ID.  
    

Questions 

**1. Which web browser does Sarah use to browse the web?** 
    

- To determine which web browser Sarah uses, we will open the Sysmon Event Viewer and filter to Event ID 1 and filter the user to Sarah to reduce the amount of source information.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/124.png) 

- Next, we will comb the files and determine which browsers were used.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/125.png) 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/126.png)

Answer: Google Chrome 

**2. Which file did Sarah download from the browser?** 
- Since we already in the filter from question 1, we can observe what file Sarah had downloaded.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/127.png)

	 

Answer: C:\Users\sarah.miller\Downloads\ckjg.exe 

**3. Which URL was the file downloaded from?**  
- To determine which URL was the file downloaded from, we will use Sysmon Event ID 15. 
- Event ID 15 is associated with file stream creation [Sysmon - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/128.png) 

Answer: [hxxp://gettsveriff[.]com/bgj3/ckjg[.]exe] <--- DO NOT CLICK ON THIS LINK

Sysmon: Files and Network 

|   |   |   |
|---|---|---|
|Event ID|Security Log Alternative|Event Purpose|
|11 / 13 (File Create / Registry Value Set)|4656 for file changes and 4657 for registry changes, both disabled by default|Detect files dropped by malware or its changes to the registry (e.g. for persistence)|
|3 / 22 (Network Connection / DNS Query)|No direct alternative, requires additional firewall and DNS configuration|Detect traffic from untrusted processes or to known malicious destinations|

**1. Which file was created by the downloaded malware to persist on the host?**  
- To identify what file was created, we will use the file to create Event ID 11.  
- First, let filter to Event ID 11 (File Create) 
- We already know that the file was created by sarah.miller, so a file path will be associated with that user.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/129.png) 

- In the screenshot below, we can see the file download (ckjg[.]exe), below must be the path to the file created by the malware 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/130.png)

**2. What is the Command & Control server malware connected to?**  
- This is concerning a network connection when it is involving a C2, so we will filter to Event 3 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/131.png) 

- We can see the malware file again, let scroll to find the IP and Port number.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/132.png) 

Answer: 193[.]46[.]217:7777 

**3. Which domain does the malicious IP correspond to?**  
- This will consist of filter to Event ID 22 (DNS) 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/133.png) 

Answer: hkfasfsafg.click 

PowerShell: Logging Commands
---

**1. Which PowerShell command was executed first?**  
- Let's navigate to the PowerShell file history to review the commands that were executed.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/134.png)

  Answer: Get-ComputerInfo 

**2. When did the Administrator run the first PS command?**  
- To see when the first command was executed, we can view the properties of the file Right-Click on the history files, select properties, and the answer should be available.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/135.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/136.png)

Answer: May 18, 2025 

**3. Find the flag that is stored in PowerShell history.**  
- To find the flag, we must go through the files of other users as well to review their PowerShell command line. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/137.png)

- after looking through “thm.alex” and “thm.bob” PowerShell Commands line history, I found the flag in “thm.bob” PS file history.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/18221697bab6b9de22b2234f0ac3afb5f8dee2d3/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20EV%20Log%20Intro/Windows%20EV%20Log%20Img/138.png)

Answer: THM{it_was_me!}
