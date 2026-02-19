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

## Manual enumeration (Inspecting source code for hidden info)
The process of manually inspecting a target application's source code (if available) or client-side code (via browser developer tools) to gather information and identify vulnerabilities. 
![Screenshot for code](/assets/code.png)
 

## Using ffuf for enumeration
ffuf stands for Fuzz Faster U Fool. It’s a tool used for web enumeration, fuzzing, and directory brute forcing.

Install ffuf
ffuf is already included in the following Linux distributions:

BlackArch
Pentoo
Kali
Parrot
Search Repology for other distributions
Note: Repology is a service that monitors a lot of package repositories and other sources and aggregates data on software package versions, reporting new releases and packaging problems.

If it’s not included in your Linux distribution you can deploy it manually following the installation instructions.
![Screenshot for ffuf](/assets/ffuf.png)

## Gobuster tool for deep enumeration
Directory/file & DNS busting tool written in Go
Gobuster is a tool used to brute-force: URIs (directories and files) in web sites, DNS subdomains (with wildcard support), Virtual Host names on target web servers, Open Amazon S3 buckets, Open Google Cloud buckets and TFTP servers.

Gobuster is useful for pentesters, ethical hackers and forensics experts. It also can be used for security tests.
![Screenshot for directory](/assets/gobuster.png)

## Apache httpd 2.4 exploits
Multiple vulnerabilities were identified in Apache HTTP Server. A remote attacker could exploit some of these vulnerabilities to trigger denial of service condition, security restriction bypass and sensitive information disclosure on the targeted system.
![Screenshot for directory](/assets/exploits.png)

