Let's scan all ports

```bash
nmap -sV -Pn -A -O 0.0.0.0
```

some generic page found only port 80 open

```bash
gobuster dir -u http://0.0.0.0 -w /usr/share/wordlists/dirb/big.txt
```

`http://0.0.0.0/webdav/`

some login page but need username i need
