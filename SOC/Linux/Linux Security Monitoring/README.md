# Linux Security Monitoring Portfolio

## Overview 
This portfolio outlines key aspects of Linux security monitoring, focusing on logging, threat detection, and incident analysis. The section include an introduction to Linux Logging and detailed insights into three threat detection techniques.

## Linux Logging Intro
This section provides foundational knowledge on logging in Linux systems. Key topics include:

**Log File Location**
- Understanding where logs are stored, primarily in the `var/log` directory.

**Types of Logs**
`auth.log`: Authentication events.
`syslog`:General system activities.
`kern.log`: Kernel-related messages.

**Basic Commands:** 
`tail`/`grep`: For monitoring and searching logs.

## Linux Threat Detection 1
This section delves into the firrst step of threat detection using Linux logs. It covers:

Initial Access via SSH
- Detection of unauthorized access attempts

SSH Brute-Force Attacks
- Analyzing patterns in the `auth.log` to identify failed login attempts.

Command Examples
- Using `grep` to filter through logs for  suspicious activities.
- Analyzing timestamps of login events to establish attack vectors.


## Linux Threat Detection 2
Building on the first threat detection method, this section focuses on: 

**Detection Techniques**
- Advance filtering of logs to detect exploitations attempts.

**Command Execution Tracking**
- Monitoring processes initiated by unauthorized users.

**Evidence Analysis**
- Utilizing logs from various sources to confirm attack paths and impact.

## Linux Threat Detection 3 
This section emphasizes on:

**Post-Exploitation Activities** 
- Identifying actions taken by attackers after paining access.

**Process Tree Analysis**
- Understanding the linage of commands executed on the systems to trace back to the original malicious action.

**Universal Detection Techniques**
- Establishing best practices for analyzing logs across different scenarios. 
