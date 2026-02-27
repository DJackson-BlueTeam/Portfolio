
**Technical Fundamentals**
**Network OSI Model (Open System Interconnection) 7 Layers** 
1. **Physical Layer**  
	- handles physical connections and transmissions of raw bit data. (cables, fiber wires, wireless) 
2. **Data and Link Layer**  
	- is a node-to-node (transfer of data between two connected devices) delivery with error detection, framing and MAC addressing. 
		- MAC (Media Access Control) Address (1A:2B:3C:4D:5E:6F) is a 48 bit that is in a group of 6 of 2 hexadecimal identifiers assigned to a NIC (Network Interface Controller) for network connection on a network. Can be used for ARP (Address Resolution Protocol) spoofing or MitM attacks by adversaries. 
3. **Network Layer**  
	- Manages routing and logical IP addresses (Internet Protocol Address) to deliver packets across the network.  
		- IPv4 (123.456.7.8): is a 32bit address space that provides 4.3 billion unique addresses that is divided into 3 parts of the IPv4.  
			- Network: identifies the network 
			- Host: Identifies the machine on the network 
			- Subnet Number: optional part of the IPv4 to divide large network into  smaller sub-networks.  
		- IPv6 (2001:0db8:0000:0000:0000:8a2e:0370:7334) or (2001:db8::8a2e:370:7334) omitting zeros: is a 128-bit address that simplifies the headers address to reduce the process by routers.  
			- Support SLAAC (Stateless Address Autoconfiguration Configuration) -  allows devices to configure their own IP address automatically without the  need of a DHCP.
  			- Domain Host Configuration Protocol: Automate the  process of configuring devices on IP networks. It allows devices to receive IP addresses and other network configurations dynamically. 
4. **Transport Layer**  
	- Provides end-to-end delivery protocols utilizing TCP and UDP. 
    
		- TCP (Transmission Control Protocol) - ensuring reliability of delivering data between applications on host communications (IP Network). This is the transport layer of the OSI that is connection-oriented.  
    
		- UDP (User Datagram Protocol) - use for time sensitive transmission (video playback or DNS lookup). This protocol is connectionless and does not guarantee delivery, order, or error checking. This is classified as a light-weight protocol.  
    
5. **Session Layer**  
	- Establishes, manages, and terminates sessions between end-user applications. Ensures that data streams are properly synchronized and managed to prevent data loss and ensure seamless communication. 

6.**Presentation Layer**  
- Translates, encrypts, and compresses data for the application layer.
- TLS (Transport Layer Security) - a cryptographic protocol, to provide secure communication over a computer network. Ensure privacy, data integrity, and authentication between two applications that are communicating. 
	
- Steps: TLS Handshake: establishes a secure connection between the client and  the server.
	- _Client Hello_: Client sends a message to the server with its supported  TLS versions, cipher suites with random numbers.
 	- _Server Hello_: Server responds with it chosen TLS version, cipher suites  with randoms numbers.
  	- _Certificate_: The server sends it digital certificate to the client for  authentication.
  	- _Server Key Exchange_: The server sends it public key or other key  exchange information.
  	- _Client Key Exchange_: The client generates a pre-master secret key,  encrypts it with the server's public key, and then sends it to the server.
  	- _Change Cipher Spec_: Both the client and server send a message  indicating that future messages will be encrypted.
  	- _Complete_: Both parties send a message to verify that the handshake  was successful, and the connection is secure.  

- SSL (Secure Socket Layer) - provides privacy, authentication, and data integrit for internet communication. Designed to encrypt data transmitted between a web server and browser to protect attacks from adversaries.
- Steps:
	- _Encryption_: SSL encrypt data to ensure privacy. If someone intercepts  the data, they will see only the jumble characters.
 	- _Authentication_: SSL initiates an authentication process called a handshake between two devices to confirm their identities.
  	- _Data Integrity_: SSL digitally signs data to ensure it hasn’t been tampered with, verifying that the data received is exactly what was sent by  the sender.
  		- JPEG (Joint Photographic Experts Group): a committee that develops and maintains various digital images standards, including the widely used JPEG image compress format.
  	 	- MPEG (Moving Pictures Experts Group): an international organization that developed standards for compression, decompression and digital representation of audio and video data.  

7. **Application Layer**
- Top layer of the OSI model that directly interacts with the end-user applications.
- This layer provides interface protocol that enables software to communicate over the networks, making sure the data is presented in a useable way while also handling compression, encryption and error control.
  - _Data Representation_: Ensure that transmitted data is in a format the receiving application can understand.
  - _Data Translation_: Converts between formats (ASCII (American Standard Code for Information Interchange) - EBCDIC (Extended Binary Coded Decimal Interchange Code))
  - _Character Encoding/Decoding_: Uses standards UTF-8 bits (Unicode  Transfer Format) or Unicode
  - _Data Compression_: Reduces file size for faster transfer.
  - _Encryption/Decryption_: Secure data.
- Network Services Access – Provides applications with access to network-based functions.
  - _Email Services_: SMTP (Simple Mail Transfer Protocol) /Port 587 sends  emails. POP3 (Post Office Protocol) /Port 995 or non-secure Port 110 and IMAP (Internet Message Access Protocol)/Port 993 incoming mails and  143 outgoing mails.
  - _File Transfer_: FTP (File Transfer Protocol) Port 21(sending commands/managing between client and server)/20(data transfer port for transferring files in active mode) TFTP (lightweight of FTP).
  -  _Web Servers_: HTTP (HyperTextTransferProtocol) port 80, HTTPS(HyperTextTransferProtocolSecure) port 443.
  -   Remote Access_: Telnet(basic) SSH (Secure Shell) port 22. 

- Application Protocols – Define rules for communication between applications
  - _File Transfer_: FTP, TFTP
  - _Web Communication_: HTTP, HTTPS
  - _Domain Name Resolution_: DNS converts domain names to IP addresses.
  - _Messaging_: XMPP "Extensible and Message Presence Protocol": Port 80/443 is good for supporting a wide range of applications including instant messaging and presence information and multipart chats. 

- Session Management – Manages and synchronizes communication sessions.
 - Session Establishment and Termination: Handles login/logout processes
 - Synchronization: Uses checkpoints for recovery in large transfers.
 - Token Management: Prevents data collisions in half-duplex systems.
 - Real-Time Communication: SIP (Session Setup), RTP (real-time media delivery) 
**DNS (Domain Name System) Look-up Processes** 
	- The DNS lookup process translates human-readable domain names into IP addresses within the network for communication.  
Steps
   1. Local Cache Check 
				- The system first checks its local DNS cache, or the host file for IP addresses. If found, the process ends.
   2. Query to Recursive Resolver 
				- If the IP is not cached locally, the query is sent to a Recursive DNS Resolver, which is provided by the user’s ISP (Internet Service Provider). This resolver i responsible for performing the full DNS resolution process.
   3. Root Name Server 
				- The recursive resolver queries one of the 13 globally Root Name Servers. These Servers do not store the IP address but direct the resolver to the appropriate TLD (Top-Level-Domain) based on the domain extension (.com, .org etc.) 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/431.png)
		
	4. TLD (Top-Level-Domain) Name Servers 
		- Provides the address of the Authoritative Name Server for the domain  
		5. Authoritative Name Server  
		- The Resolver queries the Authoritative Name Server, which holds the actual IP address of the domain. This server responds with the IP address.  
	6. aching and Response 
		- The recursive resolve caches the IP address for future queries and sends it back to the user’s device. The browser then uses this IP to establish a connection with the target server.   
		- Recursive Query: The client requests the resolver to handle the entire lookup process and return the final IP address.  
		- Iterative Query: The resolver queries each DNS server in the hierarchy step-by-step, recieving referrals until the IP address is found.  
		- Caching: Is critical in improving DNS performance. DNs server and client temporarily store resolved queries to reduce lookup times for subsequent requests. Cached records have a TTL (Time-To-Live) value, after which they expire and must be refreshed.
 
**Adversaries Abusing the DNS (Domain Name System) Look-up Processes** 
1. DNS Spoofing and Cache Poisoning (ARP Spoofing/MitM Attacks) 
	- Can introduce fraudulent DNS information into a resolver’s cache. When a user attempts to visit a website, the DNS resolver provides a malicious IP address, which redirects the use tot the site controlled by the adversary  
    
2. DNS Tunneling (nslookups/dnsteal.git/mimikatz (stealing credentials or dumping credentials) for windows systems) 
	- Use DNS protocol to bypass network security measures such as firewalls. By encoding data in DNS queries and responses, adversaries can establishe a communication channel to exfiltrate sensitive data or provide remote (C2) command-control instructions tp infect machines. DNS traffic is usually left unmonitored if the right tools are not set in place (SIEM, EDR, MDR). 
    
3. DNS Amplification (DOS (Denial of Service)/DDOS (Distributed denial of Service)) 
	- When an adversary sends small DNS queries with a spoofed source IP address (target address) to open the DNS resolver. The resolver responds with large packets to the target IP, causing overwhelming traffic to the target networks.  
    
**Types of DDOS/DOS Attacks **

1. Application Layer attacks (Layer 7)  
	- It is the very front of your infrastructure.  
    
2. DNS Server targeting Attacks  
	- Attackers send spoofed, high-volume DNS requests, sometimes levergaing amplifications techniques. 
	- Small queries can trigger a much larger response, overwhelming the DNS resolver. 
	- Floods may appear legitimate, making the filtering difficult.  
    
3. HTTP(S) Encrypted Flood  (Layer 3)
	- Botnets generate a high frequency of HTTP requests (GET, POST, DELET, PUT, etc.) to target webservers. 
	- Harder to inspect since they are encrypted data and resource intensive for the server to process 
	- The goal is to saturate the server’s ability to handle simultaneous connections, leading to denial of service for real targets.  
    
4. Protocol Attacks (Layer 3 and Layer 4) 
	- Aim to exploit weaknesses in communication protocols. These attacks usual target network infrastructure to exhaust the server and network equipment 
    
5. Ping of Deth  
	- Adversaries would send malformed ping packets that exceed permissible size. 
	- Can cause crashes or reboot systems  
	- Even though outdates, it remains a risk in poor patched systems  
    
6. Synchronous Flood  
	- Abuses the TCP (Transmission Control Protocol) three-way-handshake  
	- Sends numerous SYN packets with spoofed IPs to the server  
	- The target responds with SYN-ACK and waits for final ACK but since the source is fake, the ack never replies.  
	- Ties up Opens connection until the server connection table is exhausted.  

7. Tsunami SYN Flood 
	- A more aggressive version of SYN flood 
	- Packets are larger (1,000 bytes) increasing the load per connections 
	- Strains on the bandwidth and connection-handling capacity 

8.  Connection Exhaustion (State-Exhaustion) 
	- Adversary target devices like firewalls, load balancers, or stateful routers, exhausting their capacity to track connection states.  
	- Targeting SSL/TTL by continuously renegotiating handshakes or sending invalid packets, which can tie up stateful resources of SSL servers. 
    
9. Volumetric Attacks 
	-  Aims to consume bandwidth and saturate the network. The measure here is in bit per second(bps) which can cause attackes to scale to hundreds of gigabits or terabits per second. 
    
	      1. DNS Amplification 
			- Adversaries send DNS queries with a spoofed source IP address to open DNS resolvers. 
	      2.  UDP Flood 
			- These resolvers reply to the target with large “ANY” response packets.
          3. ICMP (Internet Control Message Protocol) 
   			- Adversary Bombard the target wit ICMP echo-request to (“ping”) packets  
			- The target attempts to reply with echo-reply, tying up resources.  
			- This type can overwhelm both bandwidth and processing capacity. 
 10. RST-FIN Flood 
	- This is a TCP-based volumetric attack using RST (reset) and FIN (finish) packets  
	-  Since FIN and RST packets are used to gracefully and forcefully shutdown connections, flooding of them can confuse TCP stacks on targeted system.   

11. Smurf Attack 
		- Targets IP broadcast networks by sending spoofed ICMP echo requests.  
		- Source IP is forged to be that of the target, so all broadcast devices reply to the victim 
		- The reply flood can overwhelm the victim’s bandwidth and processing   

**Cryptography **
	- The practice of securing communication by converting plain text into ciphertext, ensuring that only authorized parties can access the information.   derive from Greek word “kryptos” meaning hidden 
	- Confidentiality – Ensure that information can only be accessed by the intended recipient. 
	 - Integrity – Guarantees that information cannot be altered during storage or transmission without detection. 
	- Authentication – Confirms the identity of the sender and receiver, as well as the origin and destination of the information.  

**Symmetric Encryption and Asymmetric Encryption**  
1. Symmetric Encryption Algorithm (Shared-key or private-key encryption) 
	- It is a method where the same key is used for both encryption and decryption of data.  
	- Substitution method: replace element of the plaintext with other elements 
	- Transposition: The technique rearranges the order of the elements without changing them.  
    
2. Stream Ciphers 
	- RC4: known for its simplicity and speed but now considered insecure  
	- SAlasa20: Offers robust security and efficiency  
	- Grain-128: Ideal for resource-limited environment  
3. Block Ciphers  
	- AES (Advanced Encryption Standard): support key sizes of 128, 192,  and 256 bits and is widely used for secure communication. 
	- DES (Data Encryption Standard):  Uses a 56-bit key and now  considered insecure due to its small bits  
	- Triple DES: Applies DES three times to each data block, enhancing  security. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/432.png) 

4. Asymmetric Encryption Algorithm (public-key cryptography) 
	- Uses pairs of keys: a public key and a private key.  
	- The public-key is shared openly, the private-key is kept secret by the owner. 
	- Addresses key distribution and digital signatures.  
    
5. RSA (Rivest-Shamir-Adleman)
   - leverages mathematical properties of prime numbers and modular arithmetic  
	
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/433.png)
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/434.png)
	 
**Hash Values for Files and Attachments**  
	- It is a unique alphanumeric string that is generated by a cryptographic hash function based on the file’s content.  
	- Serves as a foot digital footprint that allows users to verify file integrity, detect corruption, and ensure authenticity. 
    
1. **MD5 (Message Digest Algorithm 5)**: is a 128-bit hash value from any input message. 
	- Data Integrity: Verifying that data has not been altered during transmission by comparing hash values before and after transit
	- Digital Signatures: Creating digital signatures to verify the integrity of messages or documents. 
	- Certificate Generation and Verification: Used in Public-key Infrastructure systems to generate and verify digital certificates. 
	- Password Storage: Historically used to hash passwords before storing them in databases. 
	- Checksums and File Integrity: Creating Checksum for files to detect errors introduced during transmission or storage    
	MD5 Vulnerabilities 
	- Insecure cryptographic due to collision and preimage attacks. (Integrity) 
	- Allow attackers to generate the same hash for different input (Confidentiality) 
2. **SHA-160 (Secure Hash Algorithm 1)**: is a 160-bit (20byte) hash value, commonly renders as a 40-digit hexadecimal 
	- It was widely used for various security applications and protocols, including TLS, SSL, PGP, SSH, S/MIME, and IPsec. 
3. **SHA-256 (Secure Hash Algorithm 2)**: is a 256-bit that produces a fixed-size output known as hash value or hash digest. NO matter the size, the resulting hash will be 256bits (32bytes)  
- Secure against collision and preimage attacks, making it suitable for cryptographic applications
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/435.png)
	 
**Web Servers **
1. **Nginx Web Server** 
	- High-Performance web server that functions as a reverse proxy, load balancer, and HTTP cache  
	- Known for stability rich feature set, simple configuration, and low source consumption.  
    
	1. High Performance and Scalability: designed to handle many simultaneous connections with minimal resource usage. Use an asynchronous event-driven architecture that allows it to handle thousands of connections efficiently.  
	2. Reserve Proxy and Load Balancing: act as a reverse proxy, forwarding client requests to backend servers and returning the response to the clients. Supports load balancing, distributing incoming traffic across multiple servers to ensure no single server is overwhelmed. 
	3. Caching: Nginx can cache responses from backend servers, reducing the load on these servers and improving response time for clients.  
	4. Supporting Various Protocols: Supports HTTP, HTTPS, SMTP, POP3, and IMAP, making it versatile for different types of applications.  
	5. Security Features: provides robust security features, including SSL/TLS support, HTTP/2, and HTTP/3 protocols.
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/436.png) 

2. **Apache HTTP Web Sever** 
	- Commonly referred to as httpd open-source web server developed and maintained by the Apache Software Foundation. 
	- Designed to provide a secure, efficient, and extensible server that complies with current http standards. 
		1. Security and Efficiency: robust security features and efficient performance. It supports various authentication mechanisms, SSL/TLS encryption, and access control. Designed to handle high traffic loads.  
		2. Extensibility: Supports a wide range of modules that can be added to extend functionality. The modular architecture allows users to customize the server to meet their specific needs.  
		3. Cross-Platform Support: Apache HTTP server is compatible with multiple operating systems, including Unix, Linux and windows.   
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/437.png)
	 
3. **Caddy Web Server 2015** 
	- Open Source, cross-platform web server written in Go, IT is known for it simplicity extensibility and automatic HTTPS features.  
		1. Designed to be extensible platform for deploying long running services using a unified configuration that can be updated online with REST and API  
		2. Ships with a set of standard modules, including http server, TLS, PKI, 
		3. HTTP server module is primarily used for static file server and load-balancing reverse proxy 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/438.png)
	 
4. **Lighttpd Web Serve**r 
	- Designed to be secure, fast, and standard-compliant.  
	- Optimized for environments where speed and low resource usage are critical. 
		1. Low Memory Footprint: Uses less memory, making it ideal for servers with limited resources. 
		2. Fast CGI, SCGI, CGI Support: allow web applications written in various programming languages to be used with lighttpd.
		3. Load Balancing and Proxy Support: Lightlpd can distribute incoming requests across mulitple backend servers, improving performance and reliability. 
		4. TLS/SSL Support: Supports secure connection using various TLS/SSL libraries. 
		5. Conditional URL Rewriting: The mod_rewrite module allows for flexible URL manipulation, useful for SEO and other purposes. 
		6. Virtual Heading: Supports flexible virtual hosting configurations.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/959b48439de5b291054466ef07c941673268cf3a/SOC/Basic%20SOC%20Concepts%20%26%20Frameworks/SOC%20Basics/SOC%20Basic%20Img/439.png)
	 
******Security Concepts and Frameworks******  
EDR (Endpoint Detection and Response) 
- EDR is a cybersecurity technology designed to continuously monitor endpoints (computers IoT (Internet of Things)) 
- Help organizations protect against serious cyber threats by providing visibility into malicious activities and enable automated responses to mitigate the risk. 
    
XDR (Extended Detection and Response)  
- Unified security incident platform that enhances threat detection and responses across various security tools and environments.
- Collects threat data from previously security tools, allowing easier and faster investigation and response to advance cyber-attacks. 
MDR (Managed Detection Response) 
- Proactive cyber security solution designed to protect organizations from a wide range of cyber threats, including ransomware, phishing and APT (Advanced Persistent Threats) 
- Continuous Monitoring and rapid incident response, helping organization to identify and mitigate threats before that cause damage.  

AV (Anti-Virus)Software  
- It is a security program that is designed to prevent, detect, and remove malicious software from the computer or network.  
- Modern AV protects against a wide range of threats.  

Why are EDR, XDR, and MDR better than AVS? 
- AVS is primarily a reactive software, meaning that it relies on signature base detections and does not protect against new malware or Zero-Day exploitations.  
    
Alert Triage 
	
![[440.png]] 
	
1. Alert Time – Shows alert creation time.  
2. Alert Name – Provide a summary of what happened, based on the detection rule’s name. 
3. Alert Severity – Defines the urgency of the alert (set by detection engineers)  
    
	![[441.png]]
	 

4. Alert Status – Inform if somebody is working on the alert or i the triage is done. 
    
	![[442.png]]
	 

5. Alert Verdict – Also called alert classification, explains if the alert is a real threat or noise. 
    
	![[443.png]]
	 

6. Alert Assignee – Shows the analyst that was assigned or assigned themselves to review the alert  
    

7. Alert Description – Explains what the alert is about, usually in three sections on the right. 
    

8. Alert Fields – Provides SOC analysts’ comments and values on which the alert was triggered  
    
	![[444.png]]
	 

Report Format  
	![[445.png]]
	 

Workbook Example
![[446.png]]

Response Time within a EDR (Endpoint Detection Response) 
	![[447.png]]
	 

SIEM (Security Information and Event Management) Tools 

Splunk (Leading Tool in the Market) 

-  Collect, Analyze and Correlate network and machine logs in real-time.  

	1. Splunk Forwarder – intended to monitor and collect data and direct it to Splunk instances. 
    
	2. Splunk Indexer – processes the data it receives from the forwarder.  
    
	3. Search Head in Splunk (Search and Reporting App) – users can search logs in 
		

Examples
index=<log-file>
index=<log-file> | stat count  
index=<log-file> Username=<username> 
index=<log-file> <source_ip>   
index=<log-file> Source_ip=<IPv4 ip address> 

**Elastic Stack - Elastic Search, Logstash, Beats, Kibana** 
	- A collection of different open-source tools that collect, store, search, and visualize data in real-time. 

	1. Elastic Search – full text search and analytics for JSON-formatted documents. 
    

	2. Logstash – a data processing engine that takes data from different sources, filters or normalizes it and then sends it to a destination like Kibana or any other destination for deeper analysis. 
    

	3. Beats – host-based agent that ships/transfer data from the endpoint to Elastic Search. 
    

	4. Kibana – a web–based data tool that works with Elastic Search to analyze, investigate and visualize data streams in real-time. 
![[448.png]] 

SOAR (Security Orchestration, Automation, and Response) 

Unifies all tools that are used in a SOC (SIEM, EDR, and Firewall). Can operate all tools within a single SOAR environment. 

1. Security Orchestration – link different tools within the SOAR interface  
    

2. Automation – can run tools based on the playbook the Detection Engineer place to triage potential malicious activity.  
    

3. Response – the ability to take actions based on the playbook the Detection Engineer placed to reduce the hassle on manually analyzing every bit of information from the malicious links, attachments, payload.exe and more.  
    

Example: Phishing Playbook 



Example: CVE (Common Vulnerability Exposers) Playbook 



Pyramid of Pain 

These are the initial steps an adversary may take to attempt to get a foot hold in a victim's machines whether it is a host or network based malicious operation. 

1. Hash-Values – it's critical to identify hashes to interpret rather is it a malicious activity or not.  The main hash values that can be identified are MD5 or a SHA256 using online tools – such as Virus Total.  
    

How to find hash values? There are online tools that can be used to interpret what the hash values are, or you can manually interpret the hash values through Windows PowerShell or Ubuntu/Linux terminal.   

Windows PowerShell Command – Once In Working Directory or  .\working \directory \path.doc\ 

	Get-FileHash <space>–Algorithm MD5 <Doc> 
	Get-FileHash <space>.\working \directory\ path.doc\ –Algorithm MD5 <Doc> 
	Get-FileHash <space>–Algorithm SHA256<Doc> 
	Get-FileHash <space>.\working \directory\ path.doc\ –Algorithm SHA256<Doc> 

	Unix/Linux Terminal - Once in Working Directory or /working /directory /path.doc 

	MD5sum<space>file.doc 

	SHA256sum<space>file.doc 
	 MD5sum<space>/working/directory/path/file.doc SHA256sum <space>/working/directory/path/file.doc 

2. IP Address (IPv4) – Used to identify any devices on the network rather it's a desktop, server or a remote machine  
    

3. Domain Names – Can be used to map and ip address. Domains can have a sub-domain that houses an executable link to a payload that can or maybe in the background of a html format “href” 
    

4. Network – Can be a user agent string, C2-INformation or a URL pattern followed by a HTTP.GET or HTTP.POST request that can be analyzed deeper with Wireshark or brim or manually in the Ubuntu/Linux Command Lines in Terminal.  
    

5. Tools – Attackers can use utilities to implement macro malicious documents for spear phishing or backdoor using C2 infrastructures.  
    

6. TTP (Tactic, Technique, Procedures) - includes an entire MITRE ATT&CK MATRIX. This is the rigorous phase an attacker may use.  
    
	![[450.png]]


Kill Chain  

A military concept that was adopted to use to implement a Cyber Kill Chains by LockHeed Martin. 

1. Reconnaissance Passive (no interaction) or Active (interaction) - gathering Information about the target. 
    

2. Weaponization – using the information gathered to create a tool that can be bought from the dark web or hand crafted to a specific target.  
    

3. Delivery – This is when phishing emails, malicious USB drops, or watering holes comes into action.  
    

4. Exploitation – once the delivery of the malware was double-clicked, downloaded or inserted to a USB port, when the malware was executed. 
    

5. Installation – this is when the malware is now on the victim's machine. 
    

6. C2 (Command and Control) – After the installation, there is a remote communication between the victim machine and the attacker (the external server setup by the attacker) using port 80 for http, port 443 for https or DNS tunneling. 
    

7. Actions and objectives – this is when the attacker can achieve their goals and the attackers have access to the machine such as MitM (Man-in-the-Middle), steal credentials, have permanent back-door access, monitoring, conducting a ransomware, crypto mining attack, and many more.  
    
	![[451.png]]


Unified Kill Chain – Theat Modeling  

A Unified Kill Chain is a more thorough, high-level overview of the attacker procedure of attack. It encourages threat modeling and helps interpret potential attack methodologies. There is a total of 18 steps in the Unified Kill Chain. The screenshot from TryHackMe briefly explains below.  

![[452.png]]

MITRE ATT&CK  

MITRE ATT&CK framework is a globally accessible website that is the base knowledge of an attackers Tactic, Techniques and Procedures on is based on Real-World Scenarios.  This can be used as a SOC analyst to determine how an attacker occurred and what the additional measures taken the attacker uses to reach its goals.  

![[453.png]]
