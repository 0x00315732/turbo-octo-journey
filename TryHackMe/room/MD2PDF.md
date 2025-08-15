Sure—here’s the thinking and the exact moves, without spoilers beyond the core trick.

# What’s really happening

1. **App feature:** The site takes **Markdown (MD)** and converts it to **HTML**, then a **headless renderer** (Chromium / wkhtmltopdf / WeasyPrint, etc.) turns that HTML into a **PDF**.
    
2. **Key behavior:** Many Markdown engines allow **raw HTML inside Markdown**. If that HTML contains tags that load external content—`<iframe>`, `<object>`, `<embed>`, even `<img>`—the **renderer**, not your browser, will try to **fetch those URLs**.
    
3. **Why this matters (SSRF):** The renderer runs **on the server**. So `http://127.0.0.1:5000/...` means “**the server’s own localhost**,” not yours. If there’s an **internal admin page** only reachable from localhost, the renderer can reach it and then **embed its content into the PDF**. You just read it from the generated PDF. That’s **Server-Side Request Forgery**.
    

# Practical steps I used

1. **Confirm raw HTML allowed**
    
    - Put this in the MD box:
        
        ```markdown
        # Test
        <h1>hello</h1>
        ```
        
    - If “hello” shows up in the PDF as a big heading → raw HTML passes through.
        
2. **Prove the renderer makes server-side requests**
    
    - Try loading something via HTML:
        
        ```markdown
        <img src="http://127.0.0.1:5000/">
        ```
        
    - If the PDF isn’t broken (or errors differently), it hints a server-side fetch is attempted.
        
3. **Target likely internal routes**
    
    - CTFs often hide the flag at an **admin** path on a **dev port**.
        
    - Common guesses: `:5000`, `:3000`, `:8000`, `:8080`, with `/admin`, `/flag`, `/debug`, `/status`.
        
4. **Embed the admin page into the PDF**
    
    - Use an embedding tag that shows full content:
        
        ```markdown
        <iframe src="http://127.0.0.1:5000/admin" width="1000" height="800"></iframe>
        ```
        
    - If `<iframe>` is blocked, try:
        
        ```markdown
        <object data="http://127.0.0.1:5000/admin" width="1000" height="800"></object>
        ```
        
        or
        
        ```markdown
        <embed src="http://127.0.0.1:5000/admin" width="1000" height="800">
        ```
        
5. **Read the flag inside the PDF**
    
    - The renderer fetches the internal page, paints it into the PDF, and you read it.
        

# Why it works (short version)

- Markdown → HTML → PDF renderer.
    
- Renderer **runs on the server**, so `127.0.0.1` = server itself.
    
- HTML tags that load URLs force the renderer to **pull internal pages**.
    
- The fetched content gets **rendered inside the PDF**.
    

# Mini simplified example (copy–paste)

Put this in the MD form and export:

```markdown
# Report

Internal view below:

<iframe src="http://127.0.0.1:5000/admin" width="1000" height="800"></iframe>
```

If that’s filtered, swap `<iframe>` for `<object>` or `<embed>` as above.

# If it doesn’t work

- **HTML stripped?** Some rooms sanitize HTML. Try different tags; sometimes `<img>` still fetches (won’t display HTML but can prove SSRF).
    
- **Different port/path?** Rotate through `:3000`, `:8000`, `:8080`, `/flag`, `/admin`, `/debug`, `/panel`.
    
- **Blocked by CSP?** Some renderers ignore CSP; others don’t. Changing tag type often bypasses it.
    
- **Renderer differences:** `wkhtmltopdf` vs headless Chrome can differ in what they fetch. Try multiple tags.
    

# Bonus: how to fix this (defense)

- Strip or sanitize raw HTML in Markdown (allow only safe tags).
    
- Block **localhost/169.254.169.254**/internal subnets in the renderer’s network egress.
    
- Use a **URL allowlist** (only static, public asset hosts).
    
- Run the renderer in a **sandboxed network namespace** with no access to internal services.
    

That’s the whole playbook. If you tell me what the PDF shows (blank, error, partial content), I’ll suggest the next best payload immediately.