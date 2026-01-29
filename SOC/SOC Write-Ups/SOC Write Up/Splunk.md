
**What is Splunk?** 

- One of the leading SIEM (System Information Event Management System) that allows use to collect, analyze and correlate network/machine logs in real time.  
    

**Splunk Forward** 
- A lightweight machine that is installed on the endpoint that's collects data and sends it to the Splunk Instance.  
    - Web server gathering web traffic  
    - Windows Event Logs, PowerShell and Sysmon data.  
    - Generating Host Centric Logs for Linux. 
    - Generating databases connection request, responses, and errors. 
    

**Splunk Indexer**  
- Parses and normalizes the data into field-value pairs, categorizes it and stores the result as events. 
    

**Search Head**  
- Within the search & reporting app where user can search logs and additional information within the logs.  
- When utilizing the Search Head, the user of the SIEM must be mindful that Splunk is case sensitive   
    

 

**1. How many events are presented in the log file?**  
    
![image alt](https://github.com/DJackson-BlueTeam/Portfolio/blob/3ee5c1b9a10eaf447908e03bc7c36a2dd464de04/SOC/SOC%20Write-Ups/SOC%20Write%20Up/index.png)
Answer: 2,862 
- By uploading the .json file into the Splunk indexer and accessing the log file by typing source=”VPNlogs.json” or source=”VPNlogs.json” | stats count.  
- Once the search query is completed, the results will be displayed.  

 **2. How many log events are captured by the user Maleena?**  
    
 ![[Username.png]]
Answer: 60 
- In the question, we are looking for a specific user that has generated events in the network or servers.  
- You would simply make a search using source=”VPNlogs.json” UserName=”Maleena” 
- This Manual filter identifies the specific user and the number of events that have accord in their machine.

**3. What is the username associated with the IP Address 107.14.182.38?**  
    ![[IP Address.png]]
Answer: Smith 

- Since the files are fed into Splunk, some searches – such as Ip address – can be searched by inputting the content itself in the search bar. 
- Since we are looking for a specific username, the IP address will display the user in the results, as shown above. 

**4. What is the number of events that originated from all countries except France?** 
	![[Not France.png]]
Answer: 2,814 
- For this search, you want to identify the number of events of all countries except for France.   
- In the search bar you would input source=”VPNLogs.json” country AND NOT France  
- You can also input country AND NOT France 
- This will display result of all events occurred from other countries except for France.  
    
**5. How many VPN events were associated with the IP 107.3.206.58?** 
    ![[VPNs.png]]
Answer: 14 
- Similar to question 3 - identifying the username that had the IP address 107.14.182.38, we can input the IP address into the search bar and it will populate the number of events that had occurred. 
- To search for the number of events, you would input source=”VPNLogs.json” 107.3.206.58
