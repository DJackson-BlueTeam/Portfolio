BoogeyMan2 – Spear Phishing Human Resources 

- Maxine, a Human Resource Specialist working for Quick LLS, received an application from one of the open positions in the company.  Unknown to her, the attached resume was malicious and compromised her workstation.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/354.png)
	 

- The security was able to flag some suspicious commands executed on the workstation of Maxine, which prompted the investigation. Given this, you are tasked to analyze and assess the impact of the compromise.  
    

Question: 

1. What email was used to send the phishing email?  
    
- To see what the email was used, we can use the terminal to display the “from” email. 
- cat ‘Resume - Application for Junior IT Analyst Role.eml’ | grep From 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/355.png)
	 

Answer: westaylor23@outlook[.]com 

2. What is the email of the victim employee? 
    
- We can use the same command line, but instead of grepping for “From” we will grep for “To:” 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/356.png)
	
Answer: maxine.beck@quicklogisticsorg[.]onmiscrosoft[.]com 

3. What is the name of the attached malicious document? 
    
- Now, let's grep for the attachment.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/357.png)
	Answer: Resume_WesleyTaylor.doc 

4. What is the MD5 hash of the malicious attachment? 
    
- First, we will have to save the attachment to the isolated machine we are using to conduct our investigation. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/358.png)
	 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/359.png)
	 

- We can check the hash value by using “md5sum Reume_WesleyTaylor.doc” 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/360.png)
	
Answer: 52c4384a0b9e248b95804352ebec6c5b 

5. What URL is used to download the stage 2 payload based on the document macro? 
    
- To fully get the analysis of the malicious document, we can use a command tool called “olevba” 
- olevba: is a forensic tool from oletool that was built to scan and display hidden information within documents of Microsoft office documents.  
[olevba man | Linux Command Library](https://linuxcommandlibrary.com/man/olevba) 

- We can use the command line “olveba Resume_WesleyTaylor.docs” to analysis the malicious contents.  
	
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/361.png)
	 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/362.png)
	 

Answer: hxxps://files[.]boogeymanisback[.]lol/aa2a9c53cbb80416d3b47d85538d9971/update[.]png 

6. What is the name of the process that executed the newly downloaded stage 2 payload? 
    
Answer: wscript.exe 

- Based on virus total, the “AutoOpen” is triggered once the document is opened. By doing so, it defines a file path that is directed to the ProgramData which then creates an XMLHTTP (xhttp) and an ADODB Stream (bstrm) object.  
- xhttp object is used to download the payload from the remote server using the link in the previous question.  
- Once downloaded, the xhttp saves the download as update.js in the ProgramData Directory.  
- Once the file is saved, the xhttp object creates a wscript.shell; which is an executable for the JavaScript file (‘wscript.exe C:\Program\Data\update.js’) 
    
7. What is the full file path of the malicious stage 2 payload? 
    
Answer: C:\ProgramData\update.js 
- Explain in question 6.  
    

8. What is the PUID of the process that executed the stage 2 payload? 
    
- From here we will use Volatility tool 
- Volatility: A memory forensics tool developed and maintained by the Volatility Foundation. 
- This is commonly used by malware/soc analyst and is avialbe for windows, linux, and Mac OS and is writen in Python.  
- [Home of The Volatility Foundation | Volatility Memory Forensics - The Volatility Foundation - Promoting Accessible Memory Analysis Tools Within the Memory Forensics Community](https://volatilityfoundation.org/) 
- Since we know that the executable is wscript.exe and the execution was performed on a windows machine we can analyze the results by using “vol” and have it read the file “-f” and use the Window Dynamic Link Library plugin since this attack was performed on a windows machine. Finally we can grep f260or the executable to only recieved its output  
- vol –f WKSTN.raw windows.dlllist | grep wscript.exe   
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/363.png)
	 

9. What is the parent PID of the process that executed the stage 2 payload? 
    
- Another way to look at the processes is by using the window.cmdline plugin.  
- This plugin will display all the executable commands that occured.  
- vol –f WKSTN-2961.raw windows.cmdline  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/364.png)
	 

- In this question, we are asked for the Parent Process ID of the PID of wscript.exe 
    
- Since this executable started with the malicious attachment associated with “Resume_WesleyTaylor.doc”, we can tailor our findings to the document that was attached to the email.  
    
- Vol –f WKSTN-2961.raw windows.cmdline | grep Resume_WesleyTaylor 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/365.png)
	
- - As we can see, there is a path to the malicious document from the user maxine.beck. 
-  We can also use windows.pslist which list the processes from the raw data file. 
- vol –f WKSTN-2961.raw windows.pslist  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/366.png)
	

10. What URL is used to download the malicious binary executed by the stage 2 payload? 
    
- To find the URL we know from question 5 that was a URL with the domain “boogeymanisback” with the upadter.exe 
- We can use the string command that scan binary files and we can grep for “boogeymanisback” to see the reults and should be able to find the download executable with the URL  
- strings WKSTN-2961.RAAW | grep boogeymenisback 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/367.png)
	
	 

11. What is the PID of the malicious process used to establish a C2 connection? 
    
- We know that the stage 2 payload is update.exe and we also know that the PID of wscript is 4260.  
- We can use vol -f WKSTN-2961.raw windows.pslist | grep update.exe to get the results.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/368.png)
	 
- To confirm this PID is linked with the wscript.exe pid 4260, we can display the full list and observe the outputs.  
    
- vol –f WKSTN-2961.raw windows.pslist 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/369.png)
	Answer: 6216 

12. What is the full file path of the malicious process used to establish the C2 connection?  
    
- To get this ansawer, we can use windows.cmdline to display the path and executables that occurred.  
- We can even filter for updater.exe to simplify the results 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/370.png)
	
Answer: C:\Windows\Tasks\updater.exe 

13. What is the IP address and port of the C2 connection initiated by the malicious binary?  
    
- To find the ip and port that is associated to the executable, we can use the windows.netscan and filter for update.exe 
- This is filter for updater that have the ip and port that is associated with the binary executable 
- vol –f WKSTN-2961.raw windows.netscan | grep updater.exe 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/371.png)
	
Answer: 128.199.95.189:8080 

14. What is the full file path of the malicious email attachment based on the memory dump? 
    
- Going back to question 9, we observerd a path that caused the binary executbale updater.exe to perform it sactions.  
- We can go back and observe the path again by using the windows.cmdline | grep Resume_WesleyTaylor which is the parent process of the updater.exe  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/372.png)
	
Answer: C:\Users\maxine.beck\AppData\Local\Microsoft\Windows\INetCache\Content.Outlook\WQHGZCFI\Resume_WesleyTaylor (002).doc 

15. The attacker implanted a scheduled task right after establishing the C2 call back. Wha tis the full command used by the attacker to maintain persistent access?  
- Knowing that the attacker implanted a scheduled task right after establishing a C2 call back, we can findthe path of the execution by using strings and grepping for “schtask” 
- Strings WKSTN-2961.raw | grep schtasks 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/631ded615d1855592f43cca1ad59e3e4bd73ad37/SOC/SOC%20Challenges/BoogeyMan2/BoogeyMan2%20Img/373.png)

Answer: schtasks /Create /F /SC DAILY /ST 09:00 /TN Updater /TR 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NonI -W hidden -c \"IEX ([Text.Encoding]::UNICODE.GetString([Convert]::FromBase64String((gp HKCU:\Software\Microsoft\Windows\CurrentVersion debug).debug)))\
