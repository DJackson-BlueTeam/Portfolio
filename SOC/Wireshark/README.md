## Wireshark Network Analysis Porfolio

**This repository contains a collection of network traffic analysis writeups that focuses on identifying malicious activity, understanding protocol behavior, and utilizing advanced filtering techniques within Wireshark. These projects demonstrate my ability to inspect packet-level data to support Security Operations Center (SOC) functions and incident response.** 

Network Traffic Analysis
---
**Protocol Analysis**
- Proficiency in analysing common protocols including HTTP/HTTPS, DNS, TCP/UPD, SMB, and ICMP to identify standard behavior cersus anomalies.
**Traffic Reconstruction**
- Ability to follow TCP and HTTP streams to reconstruct data exchanges and identify exfiltrated inforamtion or downloaded payloads.
**Endpoint Identification**
- Identifying source and destination assets, MAC addresses, and hostname within a packet capture (PCAP) to map topology during investigation.

Advance Filtering Syntax
---
This filtering involved isolating relevant data points quickly. Key filtering capabilities demonstrated in these writeups include:
Logic Operators
- Using `and`, `or`, and `not`  (&&, ||, !) to chain complex queries.
Conditional Filtering
- Isolating specific flags, such as `tcp,flags.syn == 1 and tcp.flags.syn == 0` to identify port scanning activity.
Protocol-Specific Queries
- `http.request.method == "POST"` to find data submimssion.
- `dns.flags.response == 0` to audit outbound queries.
- `ip.addr == [Targets_IP]` to track specific host communications.
Strings Searching
- Utlizing `contains` and `mathces` to find specific signatures or indicators of compromise (IOCs) within the packet payload. 

