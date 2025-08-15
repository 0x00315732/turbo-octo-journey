## LFI Discovery and Exploitation

While examining the HTML source and file paths of the target, I noticed the following page parameter being used:

```
http://0.0.0.0/?page=relax.php
```

### Step 1 – Testing for LFI

I attempted Local File Inclusion (LFI) by replacing the value of `page` with a directory traversal payload:

```
http://0.0.0.0/?page=../../../etc/passwd
```

The server returned the contents of `/etc/passwd`, confirming that LFI was possible.

### Step 2 – Attempting Sensitive Files

After confirming LFI, I tested for other files:

- `/etc/shadow` – Access denied
    
- `root.txt` – Not found
    
- `database` – Not found
    

### Step 3 – Finding the Flag

Through further exploration, I eventually discovered the flag file at a different path:

```
http://0.0.0.0/?page=../../../../flag.txt
```

This successfully returned the flag.

---