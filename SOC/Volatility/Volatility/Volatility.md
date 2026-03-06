**Volatility**

- Volatility is the most used framework for extracting digital artifacts from volatile memory (RAM) samples.
- Volatility is build off multiple plug-ins that are synchronous to obtain information from the memory dump. 
- Volatility was built off python 2 and python 3. This makes the installation of volatility easy for MAC, Linux and Windows Operating systems [volatilityfoundation/volatility3: Volatility 3.0 development](https://github.com/volatilityfoundation/volatility3)

**Installation Windows**
- When downloading volatility, you can used the pre-package executable (.whl file), however, only work on Windows since there is no dependencies. 
- To obtain the pre-package executables, you can download the zip file containing the application from their released page. [Release Volatility 3 1.0.1 · volatilityfoundation/volatility3](https://github.com/volatilityfoundation/volatility3/releases/tag/v1.0.1)
- To begin running the project from source, download the dependencies (python 3.5.3 or later) or "Prefile 2017.8.1 or later".

**Installation Linux** 
- to install Volatility for Linux machine you can simply execute a command with the link that is directed to the volatility program. (*git clone https://github.com/volatilityfoundation/volatility3.git*)
- To test the installation, run the vol.py file with the help parameter (*python3 vol.py -h*)
- For any Linux or MAC memory file, you will need to download the symbol files from the Volatility GitHub (https://github.com/volatilityfoundation/volatility3#symbol-tables). 

**Memory Extraction (Metal)**
- Performing memory extraction can be done in numerous ways based on the requirements of the investigation. 
- Some techniques used to perform memory dump are listed below:
	- FTK Imager
	- Redline
	- Dumpit.exe
	- win32dd.exe ? win64dd.exe
	- Memoryze
	- FastDump
- The tools above are for memory extraction with an output .raw file with some exceptions like REdline that can use its own agent and session structure.
- NOTE: when using extraxting tools on a bare-metal host, it can take a extensive amount of time. 

**Virtual Machine**
- Gathering memory file can easily be done with collecting the virtual memory file from the host machine drives. 
- The file can be change based on the hypervisor used, which are listed below:
	- VMWare: .vmem
	- Hyper-V: .bin
	- Parallels: .men
	- VirtualBox: .sav (partial memory file extraction)

**Plug-ins**
- Converting to pyhton3, the plugin structure is more accessible. 
- You just have to specify the operating system prior to specifying the plugin to be used (windows.info vs linux.info).
- Operating Systems Listed Below:
	- .windows
	- .linux
	- .mac
	Identifying Image Info and Profiles
	- Imageinfo plugin will take the memory dump and assign it a list of the best possible operating system profiles.
	- When looking to get information about what the host is running from the memory dump, we can use the following plugins list below: 
		- windows.info 
		- linux.info
		- mac.info

**Listing Processes and Connections** 
- There are 5 plugins that allows the user to dump processes and network connections, each with varying techniques used:
	- **pslist** (Process List)
		- list of processes from the doubly-linked list that keeps track of processes in memory.
		- Includes all current processes and terminated processes with their exit times. 
	- **psscan** (Process Scan)
		- locate processes by finding data structures that match _ERPROCESS. This technique helps with evasion countermeasures, it can also generate false positive. 
		- Analyst will use the plugins when a malware, mainly rootkits, attempts to hide their processes by unlinking itself from the list.
	- **pstree** (Process Tree)
		- lists all processes based on their parent process ID.
		- using the same method as pslist, this can be useful for analysts to get the full story of the processes and what may have been occurring at the time of extraction.
	- **netstat** (Network Statistics)
		- will identify all memory structures with a network connection.
	- **dlllist** (Dynamic Link Library List)
		- list all Dynamic-Link-Librarys (DLL) associated with the processes a the time of extraction. 
		- can be useful once further analysis have been completed and can filter output to a certain dll that may be an indicator of the malware. 

Volatility Hunting and Detection Capabilities
- The next two plugins are used to hunt for malware or other anomalies within a system memory.
	- **malfind**
		- will attempt to identify injected processes and thier PIDs along with the offset address and the Hex, Ascii, and Disassembly view of the infected area. 
		- This plugin scans the output of the data from the machine and identify processes that have executable bits set (RWE or RX)
	- **yarascan**
		- will search for strings, patterns and compound rules against a rule set. 
		- can use YARA file as an argument or list rules within the commandline.

Advanced Memory Forensics
- When dealing with  advance adversary, the analyst may encounter malware, maily rootkits, that will employs disrupting evasion measures that will require an analyst to dive into drivers, mutexes and hooked functions. The plugin that are used for advanced forensics are listed below.
	- **ssdt** (System Service Descriptor Table)
		- searched for hooking and output its results. 
		- ssdt is used by the windows kernel to look up systems functions. An adversary can hook into this table and modify pointers to point to a location the rootkit controls 
	- **modules**
		- adversaries will use malicious drivers files as part of their evasion.
		- will dump a list of loaded kernel modules; which can be useful in identifying active malware/ Contrarily, is the malware is idling and hidden, this plugin may miss the malware. 
	- **driverscan**
		- will scan drivers present on the system at the time of extraction.
		- This plugin help identify driver files in the kernel that the modules plugin might have missed.
		- most times, this plugin will have no output. If no results comes from the modules plugin, it may be useful to use the driverscan plugin. 
Questions:
1. What is the build version of the host machine in Case 001?
- To determine the build version of the host machine, we can use the windows.info to get the general information about the machine.
- first let navigate to the file where the machine output is stored. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/412.png)

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/413.png)

- Now that we are in the directory where the output of the machine is store, we can use a command to grab the aspects of the machine. 
- commandline: vol -f Investigation-1.vmem windows.info
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/414%201.png)

Answer: 2600.xpsp.080413-2111

- We can observe the NTbuildLab shown in the screenshot above.

2. At what time was the memory files acquired in Case 001? 
- We are still in the windows.info plugin. 
- Looking at System Time, we can see the time the memory files were acquired. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/415.png)

Answer: 2012-07-22 02:45:08
   
3. What process can be considered suspicious in Case 001?
- To discover a unfamiliar process, within the system, we can use the plugin malfind to scan to entire file for  executable ".exe" processes and review the results. 
- commandline: vol -f Investigation-1.vmem windows.malfind
- if the analyst is familiar with windows operating system processes, they will be able to point out the unusual processes within a system.
- shown below is a malicious executable that is not a windows processor and should be reported and further investigated. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/416.png)

Answer: reader_sl.exe
- also notice that the PID of the process is 1640. We should keep this in mind. 

4. What is the parent process of the suspicious process in Case 001? 
- To find the parent process of reader_sl.exe it will be difficult to different the relations of the processes through malfind plugin. 
- we must resort tot a different plugin that will display the relationship of the processes correlated to read_sl.exe
- the plugin we will be using is pstree; this will display the relationship of the processes.
- Remember the reader_sl.exe PID is 1640 (might come in handy with analyzing the results.)
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/417.png)

Answer: explorer.exe
- We can observe the malicious processor reader_sl.exe and the related PID 1640. Notice the processor explorer.exe with PID 1464 is the parent that the processor reader_sl.exe originated from. 
		
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/418.png)

5. What is the PID of the suspicious process in Case 001
- Looking back at the pstree, and malfind, we can see the same PID 1640 from both results.

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/419.png)

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/420.png)

Answer: 1640

7. What is the parent process PID in Case 001?
Answer: 1484
- we can observe from the pstree the parent process "explorer.exe" has a PID of 1484

8. what user-agent was employed by the adversary in Case 001? 
- To find out what is the user-agent of the malicious processor, we can string the results.
- strings: is a command use to find specific printable characters from a binary file. It is essentially used for reverse engineering, debugging and analyzing files that are not human readable.  
- commandline: strings Investigation-1.vmem | grep User-Agent <-- the string command will look for printable characters that are related to User-Agent and display the results. 
- strings does sensitive scanning, so correct spelling, lower-cases, uppercases etc. is necessary.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/421.png)

Answer: Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)
9. Was Chase Bank one of the suspicious bank domains found in Case 001?
- there are several was to grab the results, but we will use a command that give use the direct results of what we looking for.
- commandline: strings Investigaiton-1.vmem | grep -i https | grep chase <--- while we are using strings to look for the particular character, we can also grep the results we are looking for. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/422.png)
		
Answer: Yes
10. What suspicious process is running at PID 740 in case 002?
- To determine the process that was running at PID 740, we can use the plugin pslist and use grep to only grap the PID 740. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/423.png)
		
Answer: @WanaDecryptor@
11. What is the full path of the suspicious binary in PID 740 in Case 002?
- To look up the full path of the binary, we acn use the cmdline plugin to review the executions, and maybe come across a path.
- Since we know that @WanaDecryptor@ is the malicious binary, we can use the strings command and then grep for @WanaDecryptor@ to only get the results of that binary. 
- commandline: strings Investigation-2.raw | grep @WanaDecryptor@
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/424.png)

Answer: C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe
12. What is the parent process of PID 740 in Case 002?
- From running the previous command, we can see that task.exe had started the execution of @WanaDecryptor@.
- To be sure that tasksche.exe is the parent process of @WanaDecryptor@.exe, we can use the plugin pstree and grep for PID 740.
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/425.png)

- we can see that there is a Parent PID 1940.
- now lets use the plugin pstree, but this time, we grep for 1940 to is if the tasksche.exe is associated with the PID 1940
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/426.png)
Answer: tasksche.exe
13. What is the suspicious parent process PID connected to the decryptor in Case 002?
Answer: 1940
- Going back to the previous question, we can see the PID of the tasksche.exe
14. From our current information, what malware is presented on the system in Case 002?
- From my personal previous research about an adverarry group, that are also listed in the MITRE ATT&CK Framework. Wannacry is a known ransomeware that targets establishments for payments in bitcoin. Since the name @WanaDecryptor@ is somewhat in relation with the name, I would conclude that WannaCry is the malware. 
- But to be sure. let research the binary executable @WanaDecryptor@
[WannaCry Malware Profile | Mandiant | Google Cloud Blog](https://cloud.google.com/blog/topics/threat-intelligence/wannacry-malware-profile/)
Answer: WannaCry
15. What DLL is loaded by the decryptor used for socket creation in Case 002? 
- Windows Socket API is used for creating a socket which serves as the endpoint for communication. This is neede for network programming in Windows and allows developers to specify  teh transport protocol, address family and socket type. 
- for the Dynamic Link Library socket it would be presented as WS2_32.dll [socket function (winsock2.h) - Win32 apps | Microsoft Learn](https://learn.microsoft.com/en-us/windows/win32/api/winsock2/nf-winsock2-socket)
- we can use plugin windows.dlllist  and grep for PID 740 to filter out only the @WanaDecryptor@ processor. Then, we can look for the Window Socket DLL. 
	- commandline" vol -f Investigation-2.raw windows.dlllist | grep 740
 
	- or

	- commandline: vol -f Investigaiton-2.raw windows.dlllist | grep Ws2_32.dll
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/427.png)

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/428.png)

Answer: WS2_32.dll

16. What mutex can be found that is a known indicator of the malware in question in Case 002?
- Mutex (Mutual Exclusion) ([What is mutual exclusion (mutex) in computer programming? | Definition from TechTarget](https://www.techtarget.com/searchnetworking/definition/mutex)): is  a program that prevents multiple threads from accessing the same shared resources at the same time. It prevent multiple threads to not execute the same code simultaneously.
- we can use the plugin windows.handle to list open hadnles in the memory dump. WE can also grep for the PID 1940 since we know the parent process is the tasksche.exe with the PID 1940. 
- commandline: vol -f Investigation-2.raw windows.handle | grep 1940
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/429.png)

Answer: MsWinZonesCacheCounterMutexA

17. What plugin could be used to identify all files loaded from the malware working directory in Case 002? 
- let use commandline vol -h and see what plugin is use to identify files loaded from malware working directory. 
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/ba53ebfec29bf66ff7be74764e9758224d0bbaad/SOC/Volatility/Volatility/Volatility%20Img/430.png)

Answer: windows.filescan
