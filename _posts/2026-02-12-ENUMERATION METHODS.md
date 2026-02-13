---
title: "ENUMERATION IN HACKING"
date: 2026-02-12
categories: [enumeration techniques, tools]
tags: [web enum, methods]
---

## Netbios/SMB
Finding shared folders, printers, and user lists on Windows machines.
nbtstat, enum4linux


## SNMP
Querying Simple Network Management Protocol to find device info and traffic stats
snmpwalk, onesixtyone
 
## DNS
Forcing a "Zone Transfer" to see every internal sub-domain and IP address.
dig, dnsrecon

## HTTP/WEB
Finding hidden directories or files (like /admin or .env).
Gobuster, Dirsearch, FFUF

## SMTP
Verifying valid email addresses on a mail server.
telnet, smtp-user-enum

