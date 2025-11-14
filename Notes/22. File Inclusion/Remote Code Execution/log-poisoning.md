
# Log Poisoning

### Log Poisoning Concept

* Inject malicious PHP code into log files.
* Use LFI to include and execute the poisoned log.
* Requires read access to the targeted log file.

### Vulnerable Functions

* Execution occurs when logs are included via:

  * `include()`
  * `require()`
  * `include_once()`
  * `require_once()`

### PHP Session Poisoning

* Exploits writable PHP session files.
* Modify data stored in `PHPSESSID` cookie.
* Sessions typically stored under `/var/lib/php/sessions/` or `/tmp/`.
* Inject PHP code into session file and include it via LFI.

### Server Log Poisoning

* Poison Apache or Nginx `access.log` using headers such as:

  * `User-Agent`
  * `Referer`
  * `X-Forwarded-For`
* Include the log file via LFI to execute payload.
* Apache often stores logs under `/var/log/apache2/`.
* Nginx often stores logs under `/var/log/nginx/`.

### Other Log Poisoning Targets

* Any log accepting attacker-controlled input:

  * `sshd.log`
  * `mail.log`
  * `vsftpd.log`
* Useful LFI inclusions:

  * `/proc/self/environ`
  * `/proc/self/fd/<number>`

---

## Additional Considerations

### Real-World Scenarios

* Attackers often combine poisoning with other LFI/RCE tricks.
* Log rotation complicates exploitation.
* WAFs may block suspicious headers.

### Security Best Practices

* Validate and sanitize all user input.
* Restrict log file permissions and file inclusion functions.
* Rotate logs regularly and review them.
* Apply least-privilege to web server accounts.

### Log File Permissions

* Misconfigured permissions can allow file write/read abuse.
* Proper permission hardening is essential.

### Automation

* Attackers frequently automate poisoning + inclusion chains.

### Defense in Depth

* IDS/IPS and WAF rules can detect log poisoning attempts.
* Monitor logs for suspicious PHP-like patterns.
