**ICMP AND DNS TUNNELING**
- Traffic Tunneling (Port Forwarding) transfers data and resources to a network.  
- Provide anonymity and traffic security.  
- Adversary can use tunneling to bypass security parameters using trusted protocols that are used in everyday traffic (ICMP and DNS) 
    
**ICMP (Internet Control Message Protocol) Analysis** 

- Reports network communication issues.  
- Can be used for DoS (Denial of Services)  
- T1498.001: Network Denial of Service (https://attack.mitre.org/techniques/T1498/001/)
	- Tactic: Impact: Direct Flood 
		- An adversary may use ICMP echo requests (pings) to flood a target network bandwidth.(https://www.cloudflare.com/learning/ddos/ping-icmp-flood-ddos-attack/)
		- This consumes resources and prevents legitimate traffic from reaching the system.
		- Command Line Example: 
			- Ping: uses the ICMP protocol. A DoS is achieved by flooding the target with request faster than it can process them by using oversized packets.
				- Ping Flood (Linux/macOS): *sudo ping -f (target_ip)* <-- This sends packets a s fast as they are received or 100 times per second. This does require root/sudo privileges. 
				- Ping with Maximum Packet Size: *ping -s 65507 (target_ip)* <---The maximum IPv4 packet size (-s) is 6535 bytes. By sending large packets can strain the target's ability to reassemble fragments.
				- Rapid Interval Ping: *sudo ping -i 0.01 (target_ip)* <--- Sending requests at the subnet at the shortest possible interval (-i) 
- Can be used for data exfiltration and C2 tunneling activities. 
- T1071: Application Layer Protocol (https://attack.mitre.org/techniques/T1071/) 
	- Tactic: Command and Control
		- Adversaries may communicate with C2 server by hiding data within the payload field of ICMP echo request or reply packets. 
		- This is used to bypass firewalls that allow ICMP traffic.
- T1041: Exfiltration Over C2 channel (https://attack.mitre.org/techniques/T1041/)
	- Tactic: Exfiltration 
		- Once an ICMP-based C2 channel is established, adversaries use itt exfiltrate sensitive data from the target network to their infrastructure.  
- ICMP tunneling attacks can start after a malware execution or vulnerability exploitation.  
	- T1204: Execution (https://attack.mitre.org/techniques/T1204/)
		- Tactic: User Execution
			- Malware is executed on the system, which then initiates the ICMP tunneling client to reach the external C2.
			- There are different ways that an adversary can attempt to convince the target to perform a malicious execution 
				- Malicious Links (https://attack.mitre.org/techniques/T1204/001/)
					- Adversary using social-engineering to convince a target o click on a link to begin the execution. 
				- Malicious File (https://attack.mitre.org/techniques/T1204/002/)
					- Adversary having a persuasive phishing strategy that urge the target to download a file onto their machine that is connected to the internal network "Spear phishing Attachments".
					- These malicious files can be any file types such as .doc, .pdf, .xls, .rtf, .exe, .src, .lnk, . pif, .cpl, .reg, and .iso.
				- Malicious Image (https://attack.mitre.org/techniques/T1204/003/)
					- Image containers such as Amazon Web Services (AWS), Amazon Machine Images (AMIs), Google Cloud Platform (GCP), Azure Images, and even containers such as Dockers can be a backdoor access. 
					- Backdoor images can be upload to a public repository, and then the target may mistakenly download and the deploy the instance without knowing. 
				- Malicious Copy and Paste (https://attack.mitre.org/techniques/T1204/004/)
					- A.K.A "ClickFix", the adversary present the target with a "helpful" solution that instruct the target to copy and paste the malicious code. 
				- Malicious Library (https://attack.mitre.org/techniques/T1204/005/)
					- Adversaries may upload malware to a package managers (NPM OR PyPi), and eve public repositories. 
					- The target may then install the libraries without knowing those libraries are malicious.
- Packets can be used to establish a C2 connection (TCP, HTTP, or SSH).  

|   |   |
|---|---|
|Notes|Wireshark filters|
|Global search|- icmp|
|"ICMP" options for grabbing the low-hanging fruits: <br><br>- Packet length. <br>    <br><br>- ICMP destination addresses. <br>    <br><br>- Encapsulated protocol signs in ICMP payload.|- data.len > 64 and|

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/80.png)
 

DNS (Domain Name System) Analysis 

- Design to translate/convert IP domain addresses to IP addresses.  
- Used for exfiltration and C2 activities.  
	- T1071.004: Application Layer Protocol DNS (https://attack.mitre.org/techniques/T1071/004/) 
		- Tactic: Command and Control
			- Adversaries communicate with a C2 server by embedding data within DNS queries and responses. 
			- This involve TXT, SRV or CNAME record to pass commands.
				- TXT: Text Record that allows domain administrators to enter arbitrary text into the DNS
					- Primarily used for domain ownership verification and email security.
				- SRV: Service Records that specifies the location; specifically the hostname and the port number of servers for specified services.
				- CNAME: Canonical Name Records that maps alial name to a true or "canonical" domain name. It  acts as a forwarder . 
    
- Attacks start after a malware execution or vulnerability exploitation 
    

|   |   |
|---|---|
|Notes|Wireshark Filter|
|Global search|- dns|
|"DNS" options for grabbing the low-hanging fruits: <br><br>- Query length. <br>    <br><br>- Anomalous and non-regular names in DNS addresses. <br>    <br><br>- Long DNS addresses with encoded subdomain addresses. <br>    <br><br>- Known patterns like dnscat and dns2tcp. <br>    <br><br>- Statistical analysis like the anomalous volume of DNS requests for a particular target. <br>    <br><br>!mdns: Disable local link device queries.|- dns contains "dnscat" <br>    <br><br>- dns.qry.name.len > 15 and !mdns|

 ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/81.png)

1. Which protocol is used in ICMP tunneling? 
    

- Since we are looking for a protocol that is used in the ICMP tunneling, there are several protocols to look for – ftp, http, tcp, and ssh. 
    

- We can input a filter that filters traffic to identify which protocol is being used.  
    

- (data.len > 64) and (icmp contains “ftp” or icmp contains “ssh” or icmp contains “http” or icmp contains “tcp”) <--- this is filtering traffic with packets that have more than 64 bytes of data.  Since ICMP echo request uses small payloads, payloads larger than 64 bytes is a sign of unauthorized data being carried. And we are looking for protocols that can be used for tunneling exfiltration.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/82.png)

 

- We have 3 packets displayed. Now we must inspect each of those packets to see which protocol is being used.  
    

- From the first traffic, using cyber chef to decode the hex dump, we see multiple ssh protocols being executed. 
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/83.png)

 

- Let's look at the second hex dump to be sure that protocol ssh is still being used.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/84.png)

 

Answer: ssh 

2. What is the suspicious main domain address that receives anomalous DNS queries? (defang the address) 
    

- We can use the dns.qry.name.len > 15 mdns, however since we are looking for a suspicious domain, we should increase the size to reduce the amount of network traffic that will be displayed from the names of packets with less than 40 characters. 
    

- dns.qry.name.len > 40 && !mdns <--- filters packets with domain names greater than 40 characters and we want to exclude local devices “!mdns” (Multicast DNS) 
    

 

- We can see here that there is alot of traffic coming from IP “192[.]168[.]94[.]xxx” 
    

- Now let's decode the hex dump using cyber chef to determine what is the domain name of the adversary  
  ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/85.png)
  ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/89f8f4d6320a43626bf6c94cc5ee1c4777a42c7d/SOC/Wireshark/7.%20Wireshark%20ICMP%20DNS/ICMP%20and%20DNS/86.png)
    

Answer: dataexfil[.]com
