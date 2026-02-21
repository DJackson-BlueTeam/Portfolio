BoogeyMan3 

- Without tripping any security defenses of Quick Logistics LLC, the Boogeyman was able to compromise one of the employees and stayed in the dark, waiting for the right moment to continue the attack. Thing this initial email access, the threat actors attempted to expand the impact by targeting the CEO, Evan Hutchinson.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/374.png)
	 

- Even though the email was questionable, Evan opened the attachement despite the skepticism. After opening the attached document and seeing the nothing happened, Evan reported the phishing email to the security team.  
    
Initial Investigation 
- Upon receiving the phishing email report, the security team investigated the workstation of the CEO. During this activity, the team discovered the email attachment in the download folder of the victim.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/375.png)
	 
- In addition, the security team also observed a file inside the ISO payload.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/376.png)
	 

For this Triage, we will be using Kibana to further investigate the incident.  

1. What is the PID of the process that executed at the initial stage 1 payload? 
- First, we will filter to the date of the occurring events by setting the date range from Aug 28, 2023, to Aug 30, 2023. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/377.png)
	 
- From the document screenshot, we can see that the attached file type was a HTML file.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/378.png)
	 
- We can filter using a wildcard search to see what results will be returned and if we can file the attached document as well.  
	
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/379.png)
	 

- By typing html as a wildcard, we get 1 hit and notice (highlighted), the attached document as well. We can use a filter row by adding process.pid to see what the PID of the file is.  
- We can also see the ProcessId in the screenshot above as well.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/380.png)

Answer: 6392 

2. The stage 1 payload attempted to implant a file to another location. What is the full command-line execution? 
- In question one, we can see that when the document was opened, it executed a payload to start the lateral movement, highlighted below  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/381.png)
	 

- “c:\Windows\SysWOW64\mshta.exe” was automatically executed to start performing the next stage.  
- We can use a wildcard, again, to fileter for the “mshta.exe” and follow the timeline. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/382.png)
	 

- Here, we get 4 hits that are related to mshta.exe 
- Let’s zoom in on the next event that occurred.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/383.png)
	

- The event that occurred next, we can see that the “xcopy.exe” started another process creation.  
- We could also see this occurrence in Windows Event Sysmon Event ID 1 (Process Creation) 
- We can conclude that the command-line associated with xcopy.exe is the execution of implanting a file in another location.  
    
3. The implanted file was eventually used and executed by stage 1 payload. What is the full command-line value of this execution?  
- Remaining in the wildcard filter we are in “mshta.exe” we can continue to the next timeline event that occurred.  
- As we know, xpoy.exe was executed to implant a file in another location “\AppData\Local\Temp\review.dat” 
- Following that, another execution was made to initiate the stage 1 payload that is related to review.dat; which was targeting the DllRegisterServer (Dynamic-Link-Library Server) within the network. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/384.png)
	

4. The stage 1 payload established a persistence mechanism. What is the name of the Scheduled task created by the malicious script? 
- We are still in the wildcard filter “mshta.exe” and the last event in the timeline is related to a schedule task creation by the executed script.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/385.png)

- observing the events. “rundll32.exe” executed a scheduled task to establish persistence by forcing persistence on the machine.  

- Highlighted below, we can see the name of the Registered-ScheduledTask 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/386.png)
	
Answer: Review 

5. The execution of the implanted file inside the machine has initiated a potential C2 connection. What is the IP and port used for this connection?  
- We know that his is a C2 connection (Command and Control), which indicates ther eis a remote ip that is performing the execution to the machine.  
- PowerShell execution usually consists of dns tunneling to retrive traffic. 
- This means that information from the targets machine is being sent to a ip address that is remote.  
    

- First, we can wildcard powershell.exe since we know from the previous question that The Scheduled Task Review was executed through the powershell shown in the screen shot in question 4. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/387.png)
	 

- We can use a wild card to filter all “powershell.exe” executions and add a “destination.ip” and “destination.port ” row to see where the information is going to and where the executions are coming from.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/388.png)
	 

- There are 8,159 hits associated with “powershell.exe” 
- Also, notice that we got a hit on a destination.ip and a destination.port. 
- To be sure, we can scroll down to see if the destination.ip and destination.port are continuing to perform remote executions.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/389.png)

Answer: 165.232.170.151:80  

6. The attacker has discovered that the current access is a local administrator. What is the name of the process used by the attacker to execute a UAC bypass? 
- UAC (User Account Control): [User Account Control | Microsoft Learn](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/) 
- Is a Windows security feature designed to protect the operating system from unauthorized changes.  
- When changes to the system require administration-level permission, UAC notifies the user, giving the opportunity to approve or deny the change.  
- This consists of a process creation when a user discovers that the current access is a local administrator. 
- We need to filter process creation events (Sysmon Event Id 1) and determine what execution was done to execute a UAC bypass. 
- To find the execution, we can filter to “message: Process and .exe” <-- this filter will find all Process Creation hits that have “.exe”; which are executable that occurred.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/390.png)
	 
- We can see that we have 25 hits.  
- We can see the execution that is associated with mshta.exe that is rundll32.exe. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/391.png)
	 
- There is also a “whoami /all” which indicates the attacker wanting to know what machine they’re on.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/392.png)
	 
- Scrolling, we notice there was a “net1.exe” which tests network connectivity and displays network statistics. This indicates that the attacker is analyzing the network for lateral movement opportunities to further their attack methodology. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/393.png)
	 
- Continuing scroll, we see that a local group administrator was discovered by the attacker.  From this point, they will need access privileges to proceed with that attack. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/394.png)
	 
- We can see multiple whoami.exe occurring from the remote machine   
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/395.png)
	 

- We found a FodHelper.exe [Bypassing Defender the Easy Way – Fodhelper - TCM Security](https://tcm-sec.com/bypassing-defender-the-easy-way-fodhelper/) 
- Fodhelp.exe is a trusted binary in windows operation systems that allows elevation without requiring a UAC (User Access Control) with most UAC settings.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/396.png)

Answer: fodhelper.exe 

7. Having a high privilege of machine access, the attacker attempted to dump the credentials inside the machine. What is the GitHub link used by the attacker to download a tool for credential dumping?  
- We can filter for “dns.question.top_level_domain” to see the domains that was accessed on the machine.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/397.png)
	 

- Remember: we also know that these queries are being done through a powershell.exe for remote activity.  
- We can also filter for powershell.exe* and filter for github* 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/398.png)

Answer: https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20220919/mimikatz_trunk.zip 

8. After successfully dumping the credentials inside the machine, the attacker used the credentials to gain access to another machine. What is the username and hash of the new credential pair? 
- Knowing that the attacker used mimikatz to dump the credentials from the machine, we can filter for mimikatz.exe to refine our results and scan through to see what user access was.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/399.png)
	 

- Adjusting the results to descending order, we will see the steps made to discover a user and password of that account.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/400.png)

Answer: itadmin:F84769D250EB95EB2D7D8B4A1C5613F2 

9. Using the new credentials, the attacker attempted to enumerate accessible file shares. What is the name of the file accessed by the attacker from a remote share?  
- The attacker did all of the activity through remote access using poershell.exe. 
- We could filter for powershell.exe and also filter for files, to see all the files the attacker was able to obtain after accessing itadmin with its credentials.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/401.png)
	 

- We can see that a spike happened around 12am approaching 1am, so we can look around that timeframe to see what we can pull from the results.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/402.png)
	 
- We can start here where mimikatz was downloaded from github (in a ascending order from 12am to 1am).  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/403.png)

Answer: IT_Automation.ps1 

- Notice that there is a file in the  ITFiles directory with a file name IT_Automation.ps1 
    
10. After getting the content of the remote file, the attacker used the new credentials to move laterally. What is the new set of credentials discovered by the attacker?  
- Following the time frame of the IT_Automation file being access, we can observe that the attacker obtain a new set of username with a password. 
- We still in the “powershell.exe and file” filter. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/404.png)

Answer: QUICKLOGISTICS\allan.smith:Tr!ckyP@ssw0rd987 

11. What is the hostname of the attacker’s target machine for its lateral movement attempt?  
- WKSTN means WorkStation; which is the user's computer. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/405.png)
	 

Answer: WKSTN-1327 

12. Using the malicious command executed by the attacker from the first machine to move laterally, what is the process name of the malicious command executed on the second compromised machine?  
- While remaining in the same filter, we can add rows parent.process.name and observe the parent process of the attack in the new machine the attacker had accessed. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/406.png)

Answer: wsmprovhost.exe 

13. The attacker then dumped the hashes in this second machine. What is the username and hash of the newly dumped credentials? 
- Moving up the spike activities, we can see that the attacker used mimikatz again for credential dumping.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/407.png)
	
- In the screenshot below we can see that the attacker, while in the new user WKSTN, was able to obtain another user/password account information.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/408.png)

Answer: administrator:00f80f2538dcb54e7adc715c0e7091ec 

14. After gaining access to the domain controller, the attacker attempted to dump the hashes via DCSync attack. Aside from the administrator account, what account did the attacker dump?   
- Continuing to go up the spiked timeline, we can see the dcsyn attack occurred, shown below: 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/409.png)
	 
- At around 1:47am, still in the spike activites, we can see the attacker dump another account, shown below: 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/410.png)

Answer: backupda 

15. After dumping the hashes, the attacker attempted to download another remote file to execute ransomware. What is the link used by the attacker to download the ransomware binary?  
- Going back, I remembered I found an executable called ransomeboogey.exe in the itadmin machine. We can filter it to see what's the link the attacker used to download the ransomware. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/a515384cb2146cabbd949753aadcfe3383f76099/SOC/SOC%20Challenges/BoogeyMan3/BoogeyMan3%20Img/411.png)

Answer: http://ff.sillytechninja.io/ransomboogey.exe
