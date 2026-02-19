---
title: "HackThBox WingData Writeup"
date: 2026-16-02
categories: [nmap, enumeration, linux]
tags: [web, htb lab, pentest]
---
## INTRO
A walkthrough of the HackTheBox 'WingData' machine. This write-up covers initial access, privilege escalation, and post-exploitation techniques.

## Key Highlights
Ready to take on the WingData machine on Hack The Box? This guide provides a complete walkthrough for your penetration testing journey. While there’s no universal exploit script dedicated solely to WingData, the walkthrough will help you identify and craft custom scripts or modify public exploits as needed depending on the vulnerabilities you uncover. Here’s a glimpse of what you’ll learn:


## Initial NMAP scanning
We'll perform a basic nmap scan to see the serices, open ports and other scripts on the
machine. we'll use commands for tcp full scan.
 nmap -sC -sV -p- -T4 10.129.x.x
 
## Findings
Port 22/tcp: SSH (OpenSSH)
Port 80/tcp: HTTP (Apache/Nginx or Wing FTP Web Interface)
Port 8080/tcp: Often used for the Wing FTP Web Administration or Client Interface.

