# File Inclusion Prevention

### Input Validation

* Never pass raw user input directly into file inclusion functions.
* Design applications so users cannot control file paths.

### Whitelisting

* Use a strict whitelist mapping user input - specific allowed files.
* Implement mappings via scripts, JSON, or databases.
* This is one of the strongest defenses against LFI.

### Preventing Directory Traversal

* Use functions like `basename()` to strip path components.
* Avoid weak custom sanitization; apply recursive removal of traversal patterns.
* Sanitization must account for edge cases, normalizations, and encoding tricks.

### Web Server Configuration

* Disable remote inclusion:

  * `allow_url_fopen = Off`
  * `allow_url_include = Off`
* Restrict accessible directories using `open_basedir` or containerization.
* Disable unnecessary modules such as PHP `expect` or `mod_userdir`.

### Web Application Firewall (WAF)

* Deploy a WAF (e.g., ModSecurity) to detect and block common LFI payloads.
* Tune rules to minimize false positives.
* WAFs add a defensive layer but are **not** a substitute for proper hardening.

### Monitoring and Testing

* Hardening alone is insufficient; continuous monitoring is essential.
* Perform regular penetration tests, especially after new CVEs or zero-days are released.

---

## Additional Considerations

### Principle of Least Privilege

* Run the web server and application with minimal filesystem permissions.

### Regular Audits

* Conduct periodic code reviews and security assessments.

### Input Sanitization

* Apply proper filtering for all user inputs, not just file paths.

### Error Handling

* Disable verbose error messages in production to avoid information leakage.

### Updates and Patching

* Keep PHP, frameworks, and all dependencies fully updated.

### IDS/IPS Deployment

* Use intrusion detection/prevention systems to identify and stop malicious traffic.

### Secure Coding Practices

* Train developers to avoid dangerous file inclusion patterns and validate all inputs.
