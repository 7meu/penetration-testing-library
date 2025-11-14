# LFI and File Uploads

## Key Takeaways

### LFI + File Upload Synergy

* LFI is the primary vulnerability.
* File uploads provide a method to *place attacker-controlled files* on the server.
* Including uploaded files via LFI enables **code execution**, even if uploads appear restricted.

---

## Image Upload Technique

* Create a malicious “image” with:

  * Valid image magic bytes (e.g., JPEG header)
  * Embedded PHP code (e.g., `<?php system($_GET['cmd']); ?>`)
* Upload via normal upload feature.
* Identify the upload directory path.
* Trigger execution via LFI:

```bash
?page=../../uploads/malicous.jpg
```

---

## Zip Upload Technique

Requires PHP `zip` extension enabled.

* Create ZIP containing `shell.php`.
* Upload ZIP file.
* Include zip via wrapper:

```bash
?page=zip://uploads/shell.zip#shell.php
```

---

## Phar Upload Technique

Requires PHP `phar` extension enabled.

* Create a PHAR archive containing PHP payload.
* Upload PHAR file.
* Include:

```bash
?page=phar://uploads/payload.phar/shell.php
```

---

## Requirement: Executable LFI

* Works only when LFI calls functions like:

  * `include()`
  * `include_once()`
  * `require()`
  * `require_once()`
* Reading-only sinks (`file_get_contents`, `readfile`) **cannot execute code**.

---

## Path Discovery

To include the uploaded file, attacker must find its server-side path.

Common methods:

* Checking server responses after upload
* Directory brute-forcing
* Reviewing HTML/JS for static paths
* Observing predictable naming patterns

---

## Additional Considerations

### Real-World Behavior

* WAFs, MIME checks, and file scanners may interfere.
* Attackers combine multiple bypasses (magic bytes, polyglot files, obfuscation).

### File Type Validation

* Servers often check extensions, MIME type, and magic bytes.
* All three need to be bypassed in hardened setups.

### PHP Configuration Impact

* `zip://` and `phar://` require modules enabled.
* Disabling unused wrappers reduces attack surface.

### Magic Bytes

Useful command:

```bash
file uploaded_file
```

### Defense in Depth

* Strict whitelist-based file validation
* Store uploads *outside* the web root
* Disable dangerous functions (include wrappers, URL wrappers)
* Regular audits/pentests
* Least privilege filesystem permissions
