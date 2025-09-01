
---

Initially, I encountered an incorrect flag, but by inspecting the strings in the file, I was able to locate the correct one.

1. Extract all strings from the PCAP file:
    

```bash
strings ctf_challenge.pcap > strings.txt
```

2. Search for the flag in the extracted strings:
    

```bash
grep "STRU" strings.txt
```

> Note: Using `cat` with `grep` (`cat file | grep ...`) is unnecessary. `grep` can read the file directly, which is more efficient.

---
