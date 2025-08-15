First let's scan all ports to find open ports

```bash
namp -sV -Pn -p- -sS -O 0.0.0.0
```

There is a port 80 might be some vulnriblity but did find a page about full path discoloure

`https://www.exploit-db.com/exploits/21719`

```
80/tcp open  http    Apache httpd 2.0.54 ((Ubuntu) PHP/5.0.5-2ubuntu1.2)
```

