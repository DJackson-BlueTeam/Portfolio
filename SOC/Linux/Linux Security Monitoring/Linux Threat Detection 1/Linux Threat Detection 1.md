Detecting SSH Attacks 

- We can detect SSH attacks by listing successful SSH logins and analyze the fields. 
- *cat /var/log/auth.log | grep ‘Accepted’* <--- This is scanning the auth.log files for any results that have Accepted logins.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/221.png) 

1. When did the SSH password brutefroce start?  
- We can use the *cat /var/log/auth.log | grep ‘Accepted’* to see when the brute force login started.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/222.png)

	- notice there was a successful login with the user root (a high priviledge user that are in Linux machine) 
	- Let's check the Failed attempted logins to see if the root users that was pwn by the adversary made brute force attempts.  
	-  We will use *cat /var/log/auth.log | grep ‘Failed’* <--- this looks for results in the auth.log file that created multiple failed attempts.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/223.png)

  Answer: 2025-08-21 

2. Which four users did the botnet attempt to breach? 
- To figure out the users that the botnet attempted to breach, we can use two grep filters to identify the users.  
- We know already that one of the users is root. So let find the remaining 3 
- The filter we will use is *cat /var/log/auth.log | grep Failed | grep user* <---
- This is requesting all users that made failed login attempts on the machine.
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/224.png)

Answer: root, roy, sol, user 

3. Which IP managed to breach the root user?  
- We can go back to question 1 to identify the IP that successfully breached the root user.  
- Or we can clean up the results by specifically specifying who we are looking for (root user IP) 
- *cat /var/log/auth.log | grep ‘Accepted’ | grep root* <--- this is requesting only for the successful login of root that the adversary had breached.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/225.png)

Answer: 91.224.92.79

MITRE ATT&CK: 
T1110: Brute Force: [Brute Force, Technique T1110 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1110/) 
- Tactic: Credential Access 
	- Sub-Techniques:  

	1. T1110.001: Password Guessing ([Brute Force: Password Guessing, Sub-technique T1110.001 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1110/001/)) 
	    - An adversary may make multiple attempts to guest a password if the adversary has no knowledge of the system.  
	2. T1110.002: Password Cracking ([Brute Force: Password Cracking, Sub-technique T1110.002 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1110/002/)) 
		- An adversary may use a password cracking strategy to attempt to recover usable credentials (hash values).  
    3. T1110.003: Password Spraying ([Brute Force: Password Spraying, Sub-technique T1110.003 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1110/003/)) 
	    - An adversary attempting to use the same list of passwords towards many accounts to access at least one with the credentials the adversary has in their possession.  
    4. T1110.004: Credential Stuffing ([Brute Force: Credential Stuffing, Sub-technique T1110.004 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1110/004/)) 
	    - Adversaries can gain access to accounts by using a brute force strategy to access a system with a password, PINS, or any other protective login that is unknown to the adversary.  

Initial Access: Services 
- Linux host for public-facing services or applications (web servers, email servers, databases, etc.).  
- When one of the servers on Linux is compromised, the entire Linux of is at risk 
T1190: Exploit Public- Facing Application:[Exploit Public-Facing Application, Technique T1190 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1190/) 
- Tactic: Initial Access: 
	- An adversary will attempt to discover a weakness in the host or system to access the network.  
    - Website, servers, databases are a few examples an adversary will target to gain initial access.  
    
Using Application Logs 
- To discover breaches in a Linux system, application logs can be used to analyze activities that occurred.  
	- Web logs to detect a web attack 
	- Database logs to detect suspicious SQL queries
	- VPN logs to detect abnormal VPN login events  

Web as Initial Access 
- Any publicly exposed application can lead to a Linux breach on a vulnerable web server.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/226.png)

- Here you can see common injection that were executed by an adversary from ip 10.14.105.255 
- Ping allows code execution 
- The adversary has executed commands such as whoami and ls 
- Due to this action, the system is now at risk. 
    
1. What is the path to the Python file the attacker attempted to open.  
- To discover the path to the python file, we will grep the word ping to see any output results of the execution 
- cat /var/log/nginx/access.log | grep ping <--- this is requesting results from the access.log file filter ping commands to find the file path that is associated with the python file that the adversary is attempting to open.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/227.png)

- Here is the output of the results 
- scrolling to the ping commands, we can see the file path to the python file, shown below:  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/228.png)

Answer: /opt/trypingme/main.py 

2. Looking inside the opened file, what’s the flag you see there. 
- Since we know the path to the main.py file, lets navigate to the directory, and cat the main.py file. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/229.png)
 
- Now let's cat the main.py file to display the results.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/230.png)
	 
Answer: THM{i_am_vulnerable!} 

	
Building Process Tree 
- Process Tree Analysis is a universal approach to unwrapping the initial access.     
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/231.png)
	 

Auditd and Process Tree 
- In the screen shot above,  you can see there was a execution to discover where the adversary is currently is pivoted in the system using whoami.  
- We can further investigate the suspicious command with *ausearch –i –x whoami*  
- ausearch: A tool used to search audit daemon logs located in /var/log/audit/audit.log 
- -i: is the interpreter that translates numeric values into a huma-readable format. 
- -x: search for executable code.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/232.png)

1. What is the PPID of the suspicious whoami command? 
- If we look in the screen shot above, we can see the ppid is 1018 
	Answer: 1018 
	
2. Moving up the tree, what is the PID of the TryPingMe app?  
- We can ausearch pid=1018 to determine the the PID of trypingme.  
- Ausearch –i –pid 1018 <-- this is looking for the process that trigger the whoami.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/d5add00223aef928a9a14a737f22e31b4bfe07db/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%201/Linux%20Threat%20Detection%201%20Img/233.png)
	Answer: 577 
	BREAKDOWN 
	Find Parent of whoami 
	- pid 1020 is the the ID of the whoami command  
	- ppid 1018 is the Parent Process that tells you whatever process that have pid 1018 is the one who trigger whoami 
	Finding what PID 1018 is  
	- To find out what pid 1018 is, we ausearch –i –pid 1018 
    - We are already looking at the details for the process that launched whoami 
    - ppid 577 is the parent of process 1018 
    Identifying the TryPingMe App 
    - Whoami (*pid 1020*) was started by a shell script (*pid 1018*) 
	- The shell script (*pid 1018)* was started by the main application  
	- Looking at the log for *pid 1018*, its parent process is *557* 
	- Therefore, the TryPingMe application is *pid 577* 
1. Which program did the attacker use to open a reverse shell? 
    Answer: Python 
	- In the last screen shot in question 2, we can see that python3 was used to open the reverse shell script.
