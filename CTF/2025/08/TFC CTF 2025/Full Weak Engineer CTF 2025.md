
---

After analyzing the code, I found the bug: if a value containing a dot (`.`) is given and then converted to Base64, the application ignores it and directly gives the flag.

Here’s the relevant part of the code:

```python
@app.route("/dashboard")
def dashboard():
    username = request.cookies.get("username")
    uid = request.cookies.get("uid")

    if not username or not uid:
        return redirect("/")

    try:
        user_id = base64.b64decode(uid).decode()
    except Exception:
        return redirect("/")

    if re.match(r"user.*", user_id, re.IGNORECASE):
        role = "USER"
    elif re.match(r"guest.*", user_id, re.IGNORECASE):
        role = "GUEST"
        
    # ------------------------------------------------
    # Vulnerable part
    elif re.match(r"", user_id, re.IGNORECASE):
        role = f"{FLAG}"
    # ------------------------------------------------
    else:
        role = "OTHER"

    return render_template_string(dashboard_page, user=username, uid=user_id, role=role)
```

The key issue is this empty regex `re.match(r"", user_id, re.IGNORECASE)` — it matches everything, which can be exploited to get the flag.

### How to exploit it:

1. Log in as an admin and open your browser’s developer tools → Network → Cookies.
    
2. Replace the current `uid` cookie with the value:
    

```
Lg==
```

3. Reload the page.
    
4. The `role` will now be set to the flag.
    ![[Pasted image 20250829163319.png]]![[Pasted image 20250829163945.png]]

---
