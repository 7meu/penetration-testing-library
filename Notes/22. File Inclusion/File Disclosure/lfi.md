# Local File Inclusion (LFI)

### Basic LFI
Happens when user input is directly passed into a file include function.

```php
include($_GET['language']);
```

This allows reading arbitrary local files by modifying the parameter.

---

### Path Traversal

Escapes restricted directories using `../` sequences.

```php
include("./languages/" . $_GET['language']);
```

Attackers can break out of the intended folder:

```
?language=../../../../etc/passwd
```

---

### Filename Prefix Handling

If the application prepends a prefix, using `/` can nullify it.

```php
include("lang_" . $_GET['language']);
```

Bypass:

```
?language=/../../etc/passwd
```

The prefix (`lang_`) becomes a directory.

---

### Appended Extension Bypass

If the application appends `.php`, attackers can try:

```php
include($_GET['language'] . ".php");
```

Bypasses include:

* **Null byte injection** (old PHP only):
  `../../etc/passwd%00`
* **PHP filter wrappers**:

  ```bash
  php://filter/convert.base64-encode/resource=index.php
  ```

---

### Second-Order LFI

Input is stored somewhere (e.g., DB), then later included.

```php
$username = getUsernameFromDB($_GET['id']);
include($username);
```

Shows why *all* user-controlled input must be sanitized — not just request parameters.

---

### Platform Agnostic

LFI applies across PHP, Node.js (`fs.readFile`), Python (`open()`), Java (`FileInputStream`), etc.

---

## Additional Considerations

### Null Byte Injection *(Legacy)*

Old PHP allowed:

```
/etc/passwd%00.php
```

Modern PHP blocks this, but useful for legacy assessments.

---

### PHP Wrappers & Filters

Used to read source code even if code is normally executed.

```bash
php://filter/convert.base64-encode/resource=index.php
```

---

### Log File Poisoning (Leading to RCE)

If logs contain attacker-controlled strings, LFI can execute them.

Example injection points:

* Apache access logs
* SSH auth logs
* Email logs

Attack flow:

1. Inject PHP code into a log entry.
2. Use LFI to include the log file.
3. Code executes.

---

### Error Handling

Verbose error messages expose filesystem structure and useful paths.

Mitigation:

* Disable display_errors in production
* Use custom error pages

---

### Input Sanitization

Best practices:

* Whitelist filenames or use an allow-list of templates.
* Block `../`, `%2e%2e`, double encoding, null bytes.
* Never trust user input for file paths.

---

### Security Headers

Not a direct LFI fix, but improves overall security posture.

Headers like:

* `X-Content-Type-Options: nosniff`
* `Content-Security-Policy`
* `X-Frame-Options`

---

Mitigation requires **strict server-side validation, path whitelisting, and safe coding practices**.
