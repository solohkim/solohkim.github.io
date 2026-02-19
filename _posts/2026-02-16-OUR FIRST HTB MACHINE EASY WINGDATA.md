---
title: "HackThBox WingData Writeup"
date: 2026-02-16
categories: [nmap, enumeration, linux]
tags: [web, htb lab, pentest]
---
## INTRO
A walkthrough of the HackTheBox 'WingData' machine. This write-up covers initial access, privilege escalation, and post-exploitation techniques.

## Key Highlights
Ready to take on the WingData machine on Hack The Box? This guide provides a complete walkthrough for your penetration testing journey. While there’s no universal exploit script dedicated solely to WingData, the walkthrough will help you identify and craft custom scripts or modify public exploits as needed depending on the vulnerabilities you uncover. Here’s a glimpse of what you’ll learn:

## Ping IP
Before we start scanning we'll ping our machine on the network we see
if it's reacheable via the vpn
![Screenshot for ping](/assets/ping.png)

## Initial NMAP scanning
We'll perform a basic nmap scan to see the serices, open ports and other scripts on the
machine. we'll use commands for tcp full scan.
 nmap -sC -sV -p- -T4 10.129.x.x
 ![Screenshot for ping](/assets/nmap.png)
 
## Findings
Port 22/tcp: SSH (OpenSSH)
Port 80/tcp: HTTP (Apache/Nginx or Wing FTP Web Interface)
Port 8080/tcp: Often used for the Wing FTP Web Administration or Client Interface.

## Adding wingdata to our host
Since we got an error when we were adding our ip directly to access http at port 80,
i went ahead and added the wingdata.htb to our main host
using 
sudo nano /etc/hosts
 ![Screenshot for host](/assets/nano.png)

## Navigating to webpage
We found port 80 open and we all know if http exist we have a website running so i decided to
check the http service on the web and found the webpage
 ![Screenshot for web](/assets/wingdata.png)

