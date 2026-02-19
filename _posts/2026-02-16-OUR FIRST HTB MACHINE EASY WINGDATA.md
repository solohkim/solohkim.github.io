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


## Web Enumeration

Navigating to the web interface, we are greeted by the Wing FTP Server dashboard. Wing FTP is a cross-platform server supporting FTP, HTTP, FTPS, and SFTP.

Clicking the “Client Portal” button redirects to a new virtual host at the ftp domain revealing a login page.

At the bottom, the application discloses its fingerprint: Wing FTP Server v7.4.3.

Key observations:

    Version identification is critical. Looking at the bottom of the page or the HTTP headers often reveals the specific build.
    On WingData, the version is typically found to be prior to 7.4.4, which is a “red flag” for the recently disclosed CVE-2025-47812.
 ![Screenshot for directory](/assets/ftpexploit.png)

 ## The Vulnerability Mechanics

Wing FTP Server handles session data by creating Lua-based session files on the local filesystem. When a user attempts to log in, the server processes the username and stores it within a Lua table.
By injecting a null byte, an attacker can prematurely terminate the intended string and “break out” of the Lua string variable. This allows the attacker to append arbitrary Lua code that the server will execute when it next loads or processes the session file.

## Vulnerability Analysis

This vulnerability enables pre-authentication remote code execution, leading to full system compromise. Although Wing FTP allows anonymous logins without a password, reverse engineering reveals a deeper flaw in how sessions are stored.
The core issue lies in how session data is persisted. Instead of safe serialization formats (JSON, database records), Wing FTP writes sessions as Lua scripts that are later executed by the server using dofile("session_file").
A typical session file looks like:
Session = {
  username = "user",
  ip = "1.2.3.4",
  homedir = "/home/user",
  ...
}

Because dofile treats this file as executable code, fields such as username become injection vectors.

## The Null Byte Discrepancy
Internally, Wing FTP mixes C/C++ string handling with Lua string processing.

  

## Crafting the Payload

The payload requires two parts:

    Injection: A POST request to loginok.html with a malicious username.
    Trigger: A secondary request to an authenticated page (like dir.html) that forces the server to load the poisoned session file.


## Executing the Foothold

    Set up a listener: nc -lvnp 4444.
    Send the malformed login request via Burp Suite or a custom Python script.
    Access a session-linked page.
    Result: A reverse shell as the user running the Wing FTP service (usually wingftp or www-data).

    Vulnerability: PATH_MAX Overflow (CVE-2025-4517)

## The vulnerability CVE-2025-4517 exploits an edge case in os.path.realpath

    Theory: The data filter relies on os.path.realpath() to canonicalize paths and check if they stay within the staging directory.
    The Flaw: If a path (specifically a chain of symlinks) exceeds the filesystem’s PATH_MAX (typically 4096 bytes), realpath() may return an incomplete result without raising an error.
    The Bypass: The filter sees the incomplete path (which looks safe) and allows extraction. The actual filesystem extraction then follows the full path, including our malicious symlinks that point outside the directory.


## Internal Enumeration and User Pivot

Once inside, the environment feels restrictive. Standard enumeration tools like linpeas.sh are essential here.
Hunting for Hashes



Search for user-specific files or database dumps (SQLite is common for Wing FTP). Cracking the discovered hash (often a standard MD5 or SHA variant) will yield the credentials for a local user.

## SSH Access

With the cracked password, move from the unstable web-shell to a stable SSH session:




## Escalating to Root (The Script Analysis)

The final hurdle is escalating from a regular user to the root administrator. This is where WingData tests your ability to read and understand code logic.
Identifying the Vulnerable Component

Check for sudo privileges: sudo -l. Often, you will find a specific script that the user can run with elevated privileges, or a cron job running in the background.


## Identifying the Vulnerable Component

Check for sudo privileges: sudo -l. Often, you will find a specific script that the user can run with elevated privileges, or a cron job running in the background.

On WingData, keep an eye out for a script that handles data processing or backup routines.

## Exploitation Strategy
If the script allows you to write content to a location of your choice, the easiest path to root is:

    SSH Key Injection: Write your public SSH key to /root/.ssh/authorized_keys.
    Sudoers Edit: (If you can write with absolute precision) Add your user to the /etc/sudoers file.

## Achieving Root

After exploiting the script to place your SSH key in the root directory:







   


