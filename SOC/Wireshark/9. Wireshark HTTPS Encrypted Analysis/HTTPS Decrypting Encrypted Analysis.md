Encrypted Protocol Analysis: Decrypting HTTPS Write Up
---

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
|"HTTPS Parameters" for grabbing the low-hanging fruits||
|Request: Listing All Request|`http.request`|
|TLS: GLobal TLS Search|`tls`|
|TLS Client Request|`tls.handshake.type == 1`|
|TLS Server Response|`tls.handshake.type == 2`|
|Local Simple Service Discovery Protocol (SSDP) <br><br> SSDP: is a network protocol that provides advertisement and discovery of network services.|`ssdp`|

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/100.png) 

- Like a TCP three-way-handshake, a TLS also has handshakes between the client and the server.  
- Client Hello: `(http.request or tls.handshake.type == 1) and !(ssdp)` <--- this is to filter the start of the conversation between the client and the server for both unencrypted (http) and encrypted web traffic (https) and ignores the ssdp (Simple Service Discovery Protocol) that is used by devices such as smart tv’s, printers, routers , etc.  
- Server Hello: `(http.request or tls.handshake.type == 2) and !(ssdp)` <--- this is to filter out responses from the server during an unencrypted or an encrypted connection. This allows us to see the communication of a connection by capturing the client's intent for http or the server response for the TLS. 
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/101.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/102.png) 

Encrypted Key Log Files
---

- It is a text file that has unique key pairs to decrypt the encrypted traffic session. 
- These key pairs are automatically created when a connection is established with an SSL/TLS-enabled webpage.   

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/103.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/104.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/105.png) 

1. **What is the frame number of the “Client Hello” message sent to “account.google.com”?** 
- We will input the filter associating with the client sending a request during a TLS handshake 
- `(http.request or tls.handshake.type == 1) &&  !ssdp` <--- filtering the start of a web connection and isolating additional traffic.  

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/106.png)

2. **Decrypt the Traffic with the “KeysLogFile.txt” file. What is the number of HTTP2 packets?**  
- First, we need to add the KeyLogFile.txt 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/107.png) 

![alt txt](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/108.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/109.png) 

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/110.png) 

Answer: 115 

- There is a total of 119 packets, but notice there are 4 TLSv1.3 packets. We need to deduct the 4 from the 119 displayed packets.  

3. **Go to Frame 322. What is the authority header of the HTTP2 packet? (Defanged the address)**
- On the same http2 traffic go to frame 322 – > Hypertext Transfer Protocol 2 -> Stream Headers -> Headers  
    
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/111.png)

4. **Investigate the decrypted packets and find the flag. What is the flag?**  
- To find the flag, I filtered to http, since the file should be a readable file. I found a few results  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/112.png)

 

- Here there is a text/plain file from packet 1644 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/5824daf5f94ba6e27f8fa41fbe93921a8135fced/SOC/Wireshark/9.%20Wireshark%20HTTPS%20Encrypted%20Analysis/HTTPS%20Img/113.png)

Answer:  FLAG{THM-PACKETMASTER}
