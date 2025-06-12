
---

### 🔐 **Step 1: Connect to the SSH server**

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

- **`ssh`**: A command that opens a secure connection to a remote computer.    
- **`bandit1@bandit.labs.overthewire.org`**: You are logging in as the user `bandit1` on the server `bandit.labs.overthewire.org`.
- **`-p 2220`**: This tells SSH to use port `2220` instead of the default port `22`.

➡️ This command connects you to the Bandit server as user `bandit1`.

---

### 📄 **Step 2: Display the contents of a strangely named file**

```bash
cat ./-
```

- **`cat`**: Displays the contents of a file.
- **`./-`**: Refers to a file named `-` (just a single dash), located in the current directory.

🔍 **Why `./-` and not just `cat -`?**

Normally, `cat -` tells the terminal to read from **standard input** (i.e., wait for you to type something). But in this case, `-` is the **name of a file**, not a command option.

- `./` explicitly says "look for the file in the current directory."
- So `cat ./-` tells the system: _"I mean the file actually named `-`, not the option `-`."_

➡️ This shows the contents of the file `-`, which contains the password for the next level.

---
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
