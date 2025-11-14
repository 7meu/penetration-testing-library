# PHP Filters

## Key Takeaways

### PHP Wrappers
Provide access to different I/O streams in PHP via the `php://` scheme. Useful for extending LFI attacks.

### PHP Filter Wrapper
Accessed through:

```

php://filter/

```

Key parameters:

- `read=` - filter to apply
- `resource=` - file to read

Most commonly used filter:

```

convert.base64-encode

```

Allows reading PHP source code instead of executing it.

---

## Reading PHP Source Code

### Example LFI Without Filters
```bash
?page=include.php
```

Renders the executed output — not the source.

### Using Base64 Filter

```bash
?page=php://filter/read=convert.base64-encode/resource=index.php
```

Then decode:

```bash
base64 -d output.txt
```

---

## Useful Filters (Most Relevant)

* `convert.base64-encode`
* `convert.base64-decode`
* `string.strip_tags`
* `string.toupper`
* `string.rot13`

Note: Only base64-encode meaningfully aids LFI exploitation.

---

## Fuzzing for PHP Files

Discover PHP files that can be used for inclusion:

```bash
ffuf -w wordlist.txt -u http://target.com/FUZZ.php -mc all
```

Also enumerate referenced files within reachable source code.

---

## Standard Inclusion vs Source Disclosure

* **Standard LFI:** Executes PHP - returns HTML.
* **Filter-based LFI:** Reads PHP - returns raw source (base64).

Source disclosure is essential for understanding logic flaws and finding deeper vulnerabilities.

---

## Additional Considerations

* Other encoding/obfuscation approaches may bypass filters.
* Errors can reveal full file paths or partial source lines.
* Scripts can automate discovery and decoding workflows.
* Always apply least privilege and avoid dynamic file inclusion in development.
