# PHP Version Exploit — Documentation & Steps

## Summary

This document records the steps taken to discover a vulnerable PHP service, identify an applicable public exploit, run the exploit, and retrieve the flag. Use this as a reproducible write-up for CTFs or authorized penetration tests.

## Environment

- Target: (redacted) — target IP or hostname used during the engagement
    
- Attacker machine: (redacted) — tooling environment
    
- Tools used: `nmap`, web browser, `exploit-db` reference
    

## Objective

Enumerate services and versions, find a known exploit for the identified PHP version, run the exploit (authorized engagement / CTF), and retrieve `flag.txt`.

## Commands & Steps

1. **Service discovery and version enumeration**
    

```bash
# Run version scan without host discovery (assumes host is reachable)
nmap -sV -Pn <target>
```

_Result:_ nmap output showed a PHP-based service and reported a specific PHP version.

2. **Vulnerability research**
    

- Using the exact PHP version string from the `nmap` output, search public exploit databases (Exploit-DB, CVE lists) for matching exploits.
    

Example reference found:

```
https://www.exploit-db.com/exploits/49933
```

3. **Exploitation (summary)**
    

- Obtained the exploit reference and adapted its usage to the target context. Note: exploit usage details are intentionally omitted from this document — keep exploitation steps limited to authorized engagements.
    

4. **Post-exploitation and flag retrieval**
    

```bash
# After successful exploitation, read the flag file
cat /flag.txt
```

_Result:_ Flag contents printed to terminal.

## Evidence & Notes

- Store terminal session logs, `nmap` output, and any proof-of-exploit artifacts in a secure location for reporting.
    
- Always verify authorization before running exploits against systems that are not yours.
    

## Remediation & Recommendations

- Patch or upgrade PHP to a version that addresses the specific CVE referenced by the exploit.
    
- Apply application-level hardening and least-privilege configurations for web-facing services.
    
- Regularly scan and monitor for outdated components.
    

## References

- Exploit-DB entry: `exploit-db.com/exploits/49933`
    
- Nmap service/version detection (`-sV`) documentation
    

---
