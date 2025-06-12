
---

### **Step 1: Connect to the remote server via SSH**

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

- **`ssh`**: This stands for "Secure Shell" – it's a protocol used to securely log into remote computers over a network.
- **`bandit0@bandit.labs.overthewire.org`**: This means you're logging in as the user `bandit0` on the server `bandit.labs.overthewire.org`.
- **`-p 2220`**: This specifies the **port number** to connect to. Normally, SSH uses port `22`, but this server uses `2220`.

So this command securely logs you into the **Bandit wargame server** as user `bandit0`.

---

### **Step 2: Read the contents of a file**

```bash
cat readme
```

- **`cat`**: A command that reads and displays the content of a file.
- **`readme`**: The name of the file you want to view.

So this command shows the contents of the file named **`readme`**, which likely contains the password for the next level of the game.

---

ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
