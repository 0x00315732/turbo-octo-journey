
---
### 🔐 **Step 1: Connect to the SSH server**

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
```

- **`ssh`**: The command used to securely log in to a remote machine.
- **`bandit2@bandit.labs.overthewire.org`**: This means you're logging in as user `bandit2` on the server `bandit.labs.overthewire.org`.
- **`-p 2220`**: You're connecting using port **2220** instead of the default SSH port (22).

➡️ This command logs you into the Bandit server as user `bandit2`.

---

### 📄 **Step 2: Read the contents of a file with spaces in its name**

```bash
cat ./spaces\ in\ this\ filename
```

- **`cat`**: Displays the contents of a file.
- **`./spaces\ in\ this\ filename`**:
    - `./` means "in the current directory."
    - The filename is **`spaces in this filename`**, which includes spaces.
    - The backslashes (`\`) are used to **escape the spaces**, so the shell understands it's one single file name, not multiple arguments.

✅ So this command shows the contents of a file literally named **`spaces in this filename`**.

---
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
