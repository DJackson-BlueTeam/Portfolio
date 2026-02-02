**Encrypted Protocol Analysis: Decrypting HTTPS Write Up** 

- Sometime, malicious activity is also done in HTTPS (Hypertext Transfer Protocol Secure) 
- HTTPS encrypts the data while it is being transferred to the network. 
- Without having the encryption and decryption pairs, it will be difficult to read what is being transferred.  
T1573.002: Encrypted Channel - Asymmetric Cryptography ([Encrypted Channel: Asymmetric Cryptography, Sub-technique T1573.002 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1573/002/))
- Tactic: Command and Control
	- Adversaries leverage HTTP/HTTPS to communicate with systems under their control, blending with legitimate web traffic to bypass firewalls and avoid detection
T1567.002: Exfiltration Over Web Service: Exfiltration to Cloud Storage ([Exfiltration Over Web Service: Exfiltration to Cloud Storage, Sub-technique T1567.002 - Enterprise | MITRE ATT&CK®](https://attack.mitre.org/techniques/T1567/002/)) 
- Tactic: Exfiltration
	- Adversaries use HTTPS-based cloud services to exfiltrate data from a victim network. 

|   |   |
|---|---|
|Notes|Wireshark Filter|
|"HTTPS Parameters" for grabbing the low-hanging fruits: <br><br>- Request: Listing all requests <br>    <br><br>- TLS: Global TLS search <br>    <br><br>- TLS Client Request <br>    <br><br>- TLS Server response <br>    <br><br>- Local Simple Service Discovery Protocol (SSDP) <br>    <br><br>Note: SSDP is a network protocol that provides advertisement and discovery of network services.|- http.request <br>    <br><br>- tls <br>    <br><br>- tls.handshake.type == 1 <br>    <br><br>- tls.handshake.type == 2 <br>    <br><br>- ssdp|

![[100.png]] 

- Like a TCP three-way-handshake, a TLS also has handshakes between the client and the server.  
- Client Hello: (*http.request or tls.handshake.type == 1*) and !(*ssdp*) <--- this is to filter the start of the conversation between the client and the server for both unencrypted (http) and encrypted web traffic (https) and ignores the ssdp (Simple Service Discovery Protocol) that is used by devices such as smart tv’s, printers, routers , etc.  
- Server Hello: (*http.request or tls.handshake.type == 2*) and !(*ssdp*) <--- this is to filter out responses from the server during an unencrypted or an encrypted connection. This allows us to see the communication of a connection by capturing the client's intent for http or the server response for the TLS. 
    

![[101.png]] 

![[102.png]] 

Encrypted Key Log Files  

- It is a text file that has unique key pairs to decrypt the encrypted traffic session. 
- These key pairs are automatically created when a connection is established with an SSL/TLS-enabled webpage.   

![[103.png]] 

![[104.png]] 

![[105.png]] 

1. **What is the frame number of the “Client Hello” message sent to “account.google.com”?** 
- We will input the filter associating with the client sending a request during a TLS handshake 
- (***http.request or tls.handshake.type == 1***) *&&  !ssdp* <--- filtering the start of a web connection and isolating additional traffic.  

	![[106.png]]

2. **Decrypt the Traffic with the “KeysLogFile.txt” file. What is the number of HTTP2 packets?**  
- First, we need to add the KeyLogFile.txt 
![[107.png]] 

![[108.png]] 

![[109.png]] 

![[110.png]] 

Answer: 115 

- There is a total of 119 packets, but notice there are 4 TLSv1.3 packets. We need to deduct the 4 from the 119 displayed packets.  

3. **Go to Frame 322. What is the authority header of the HTTP2 packet? (Defanged the address)*
- On the same http2 traffic go to frame 322 – > Hypertext Transfer Protocol 2 -> Stream Headers -> Headers  
    

![[111.png]]

4. **Investigate the decrypted packets and find the flag. What is the flag?**  
- To find the flag, I filtered to http, since the file should be a readable file. I found a few results  
![[112.png]]

 

- Here there is a text/plain file from packet 1644 
![[113.png]] 

Answer:  FLAG{THM-PACKETMASTER}