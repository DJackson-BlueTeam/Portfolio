## Windows Security & Threat Detection Porfolio

Overview
--
This repository documents my technical skills in WIndows environments, tailoring focus on log analysis through **Windows EventViewer/Sysmon** and systematic methods for **Threat Detection**. These projects demonstrate my ability to monitor health, identify security anomalies, and respsond to potential compromises within a Windows-based infrastructure.


## 🛡️ Windows Event Viewer: Core Log Analysis
A deep dive into the Windows logging system to monitor system events and security-related activities. 
---



**Key Competencies:**

- **Log Management**: Expert navigation of Application, Security, Setup, System, and Forward Event logs.
- **Audit Policy Implementation**: Configuring "Audit Object Access" and "Audit Logon Events" to generate percise security data.
- **Filtering & Custom Views**: Creatin XML-based filters to isolate specific Event ID.
- **Operational TRoubleshooting**: Utilizing System logs to identify hardware faliures, drive issues, and service timeouts.
---

## 🔍 Intro to Threat Detection Series
---
**Part 1: Foudations of Detection**
---

Focused on the baseline of security monitoring and identifying "low-hanging fruit" indicators or compromise.
---



- **Logon Analysis**: identifying brute-force attacks through high-frequency failed patterns.
- **Processs Monitoring**: Tracking `cmd.exe` and `powershell.exe` execution to detect initial access attempts.
- **User Account Changes**: Monitoring for unauthorized privilege escalation or the creation of new administration accounts.
----
Part 2: Lateral Movement & Persistence
---

Advance analysis of how attacks move throuhg a network and maintain access. 
---


- **Network Mapping**: Identifying unauthorized use of tools  like `ipconfig`, `net stat`, and `nslookup` for internal reconnaissance.
- **Service Manipulation**: Detecting the installation of malicious service or scheduled tasks used for persistence.
- **Registry Monitoring**: Identifying changes to "Run" keys and other startup locations

---
Part 3: Advance Hunting Obfuscation
---

Focusing on sophicated adversary tactic and defense evasion.
---


- **Powershell Analysis**: Decoding Base64 encoded commands and identifying obfuscated scripts designed to bypass tradtional AV.
- **Event Log Clearing**: Detecting Event ID 1102 (The log Was cleared), a critical indicator or antiforensics activity.
- **Sysmon Intergration**: Using System Monitoring (Sysmon) to gain visibility into process creations, network connections, and file version changes.

---
🛠️ Technical Toolkit
---
- **operation System**: Windows 10/11, Windows Server
- **Security Tools**: Windows Event Viewer, Sysinternals Suits (Sysmon, Process Explorer), Powershell.
- **Methodologies**: MITRE ATT&CK Mapping, Log Aggregation, Incident Response Life Cycle

---
📈 Professional Impact
---
The write-uos included in this portfolio showcase a proavtice approach to cybersecurity. By mastering these Windows internals, I will provide the organization with the ability to:

1. Reduce "Time to DEtection" (TTD) by identifying anomalies in real-time.
2. Maintain a hardened system posture through continous audit log review.
3. Provide detailed forensic evidence during incident post-mortems. 
  


