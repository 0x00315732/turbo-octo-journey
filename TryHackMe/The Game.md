### Step 1: Extract All Strings from the `.exe` File

Use the `strings` command to extract readable text from the executable:

```bash
strings Tetrix.exe > strings.txt
```

This will save all readable strings from `Tetrix.exe` into a file called `strings.txt`.

---

### Step 2: Search for Keywords or Flag Patterns

Look for common CTF flag patterns, such as `CTF{}`, `THM{}`, or similar. In my case, I searched online and found that the flag format was likely `THM{}`.

To search for this pattern in the extracted strings, use `grep`:

```bash
grep THM strings.txt
```

This command will search for any string containing `THM`, which helped me find the flag.