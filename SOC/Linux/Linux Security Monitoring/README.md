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

