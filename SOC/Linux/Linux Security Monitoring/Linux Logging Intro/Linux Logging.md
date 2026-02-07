Linux Logging Write Up 

- Contrarily to Windows logs, Linux logs most events into plain text files, meaning that logs can be read in text editors.  
- Most Linux logs are located in the var/log folder shown below:     
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/201.png) 

- *cat* <-- reads the folder and file content of */var/log/syslog*  
- | <-- this is a pipe. It takes the output of cat and passes it to the head (like tunneling). 
- *head* <-- only display the first 10 lines of the logs. Without the head command, you may get a numerous generated logs.  
Filtering Logs 
- grep is used to filter logs if you are looking for something specific.     
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/202.png) 

Discovering Logs 
- Let us interpret that we are looking for user logins.  
- We can use _ls –l /var/log_ to list what s logs are in our system  
- ls <-- list file and directories 
- -l displays detailed information for each entry  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/203.png)  

- We can also search for potential login across all logs as well using _grep  -R –E “auth|login|session” /var/log _
- grep <-- search specific text patterns 
- -R <-- is a recursive command telling the command to search through all files in the specified directory and all of it subdirectories 
- -E is a extended regular expression that allows the use of special operators like pipe “|” that act as or in this case 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/204.png) 

Questions 

1. Which time server domain did the VM contact to sync its time?  
- I believe can use grep to filter “sync” 
- Let's use *cat /var/log/syslog | grep* sync to find specific time sync 
- Looking at the output “*ntp.ubuntu.com*” seems like the time server domain after seeing it repeatedly
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/205.png)

Answer: ntp.ubuntu.com 

2. What is the kernel message from Yama in var/log/syslog? 
- We will use *cat /var/log/syslog | grep Yama*  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/206.png)
	
Answer: becoming mindful  

Authentication Logs 
- The most useful log file that should be monitored is the _/var/log/auth.log_ or _/var/log/secure_ on a RHEL-based system  
- RHEL (Red Hat Enterprise Linux) system: design for stability, security and enterprise—grade performance, sharing the same architecture and package management tools as the official Red Hat Product ([Red Hat Enterprise Linux operating system](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)) 
- Each successful logon and logoff is logged and can be seen by filtering the events containing “session opened” or “session closed” 
- Example shown below using *cat /var/log/auth.log | grep –E ‘session opened|session closed’*
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/207.png)

SSH-Specific Events 
- SSH daemon store its own log of successful and failed ssh logins.  
- These logs are sent to the same auth.log file but have a different format. 
- *cat /var/log/auth.log | grep “sshd” | grep –E ‘Accepted|Failed’* 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/208.png) 

Miscellaneous Events  

- The same log file can be used to detect user management events.  
- Only common basic linux commands are needed.  

Example:  
- useradd is a command to add a new users ([useradd(8) - Linux manual page](https://www.man7.org/linux/man-pages/man8/useradd.8.html)).  
- usermod is used to modify a user account ([usermod(8) - Linux manual page](https://man7.org/linux/man-pages/man8/usermod.8.html))  
- userdel is used to delete a user account ([userdel(8) - Linux manual page](https://man7.org/linux/man-pages/man8/userdel.8.html)) 
- *cat /var/log/auth.log | grep –E ‘(passwrd|useradd|usermod|userdel)\[‘* 
- *cat* <-- read the files 
- */var/log/auth.log* <-- the path to the file that cat will read  
- *grep –E* <-- look for specific expression within the files 
- *‘(passwrd|useradd|usermod|userdel)\[’* <-- this is the pattern that cat will read as the output if it is with the file using grep 
- *\[* <-- looks for literal opening square brackets immediately following the pattern above 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/209.png) 

- We may come across command that was launch with sudo (root privilege) 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/210.png) 

Questions 

1. Which IP address failed to login to multiple users via SSH?  
- To determine the IP that made multiple failed attempts, we will use the command below. 
- *cat /var/log/auth.log | grep “sshd” | grep Failed* 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/211.png)

Answer: 10.14.94.82 

2. Which user was created and added to the “sudo” group?  
- We will use command _cat /var/log/auth.log | grep useradd_     
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/212.png)

Answer: xerxes 

Common Linux Logs 

- There are many other logs in the /var/log directory.  
- _/var/log/kern.log_: Kernel messages and errors, useful for more advance investigations ([Chapter 13. Getting started with kernel logging | Managing, monitoring, and updating the kernel | Red Hat Enterprise Linux | 8 | Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/getting-started-with-kernel-logging_managing-monitoring-and-updating-the-kernel)) 
- _/var/log/syslog_ (or /var/log/messages): A consolidated stream of various Linux events ([16 Best Syslog Servers for Linux and Windows (Free & Paid)](https://phoenixnap.com/kb/syslog-server)) 
- _/var/log/dpkg.log_ (or _/var/log/apt_): Package manager logs on Debian-based systems ([How to find out when Debian or Ubuntu package installed or updated - nixCraft](https://www.cyberciti.biz/faq/debian-ubuntu-linux-find-package-installed-updated-date/)) ([Linux dpkg Log | Detection](https://insiderthreatmatrix.org/detections/DT044)) 
- /var/log/dnf.log (or /var/log/yum.log): Package manager logs on RHEL-based systems ([How to Use 'Yum History' to Find Out Installed or Removed Packages Info](https://www.tecmint.com/view-yum-history-to-find-packages-info/)) 

App-Specific Logs  
- In SOC monitoring programs may also be a part of the role and understanding logs is necessary.  

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/213.png) 

Bash History  
- Bash history records each command that run after pressing Enter.  
- By default, commands are first stored in memory during a session and then 
-  written to the per-user ~/ .bash_history file when a user logs out.  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/214.png) 

- There are limitations with .bash_history such as it does not track non-interactive commands (initiated by the OS, CRON jobs, or web servers). 
- It can. However, be configured ([Configuring BASH History](https://datawookie.dev/blog/2023/04/configuring-bash-history/)) 

Question 
1. According to the VM’s package manager logs, which version of unzip was installed on the system?  
- Let use the _/var/log/dpkg.log_ (package log) to determine the version.  
- cat /var/log/dpkg.log | grep unzip 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/215.png)

Answer: 6.0-28ubuntu4.1 
2. What is the flag you see in one of the users’ bash history? 
- There is only one other user beside ubuntu on this machine, which is root.  
- We may have to use sudo su to run a root to read the .bash_history 
- *cat /root/.bash_history* 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/216.png)

Answer: THM{note_to_remember} 

Audit Daemon 
- A built-in auditing solution often used for run time monitoring.  
- ([auditd/audit.rules at master · Neo23x0/auditd](https://github.com/Neo23x0/auditd/blob/master/audit.rules)) 

Using Auditd 
- Analysts can view generated logs in real time in /var/log/audit/audit.log. 
- Using *ausearch* command makes it easier to analyze logs by formatting them for readability and supports filtering options.  
- *ausearch –i –k proc_wget*  <--- used to query the Linux Audit logs for specific events. 
- *-i* (Interpreter): tells the tool to interpret numeric entities into text. 
- *-k* *proc_wget* (Key): search only show audit events that were tagged with the specific key proc_wget 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/217.png) 

- pid (Process ID) &  ppid (Parent Process ID): Helpful in linking events and building a process tree. 
- auid=ubuntu: Audit user. The account originally used to log in locally (keyboard) or remotely (ssh) 
- uid=root: The user who ran the command. The field can differ from auid if switching users with sudo or su 
- tty=pts1: Session identifier. HElps distinguish events when multiple people work on the same linux server.  
- Exe=/usr/bin/wget: Optional tag specified by engineers in auditd rules that is useful to filter the events.  
    
File Event  
- Auditd tracked the change to the /etc/ssh/sshd_config file via the nano command.  
- SOC teams set up rules to monitor changes in critical files and directories. 

Questions  
1. When was the secret.thm file open for the first time? (MM/DD/YY HH:MM:SS) 
- First, we will have to switch to a root user since some logs require the root user to access the files.  
- sudo su <--- this will switch the user to root user 
- _ausearch –i –k file_thmsecret | grep secret.thm_  <--- we are using ausearch to retrieve the log file_thmsecret information and using grep to find the specific file we are looking for.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/218.png)
	 
Answer: 08/13/25 18:36:54 
	
2. What is the original file name downloaded from GitHub via wget? (Wget process creation is logged with the “proc_wget” key) 
- The command to generate the general information is the same as question 1, however we are looking for proc_wget instead.  
- Since the file was downloaded from github, the protocol used for websites is usually https, so we can grep https to simplify the search.   
- *ausearch –i –k proc_wget | grep https*  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/54e9f28feb7278ba04ec12ca461c14f6f30e6756/SOC/Linux/Linux%20Security%20Monitoring/Linux%20Logging%20Intro/Linux%20Logging%20Img/219.png)

Answer: naabu_2.3.5_linux_amd64.zip 

3. Which network range was scanned using the downloaded tool?  
- In linux logging, EXECVE record is generated whenever a process has started.  
- We can see in the screenshot above type=EXECVE was logged, so we can ausearch it with –m (a message type). 
- Then we can grep for naabu to reduce the amount of information that will be provided.  
- *ausearch –m EXECVE | grep –i naabu*  
    
