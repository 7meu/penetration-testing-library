# IIS Tilde Enumeration

## 1. Concept Overview

* IIS generates **8.3 short file names** for backwards compatibility.
* The short names can be accessed using the **tilde (~)** character in URLs.
* This behavior can expose hidden files and directories.

### 8.3 Naming Format

* Consists of **8 characters + tilde + number + 3-character extension**.
  * Example directory: `SecretDocuments` - `SECRET~1`
  * Example file: `somefile.txt` - `SOMEFI~1.TXT`

### Affected IIS Versions

* **5.0 - 8.5** (Windows Server 2000 - 2012 R2)

---

## 2. Enumeration Process

1. Send crafted HTTP requests containing `~` and letter patterns.
2. Analyze responses for valid files (status `200 OK`) or restricted directories (`403 Forbidden`).
3. Infer short names and attempt to reconstruct full names.
4. Use directory brute-forcing to confirm and expand findings.

---

## 3. Tools and Commands

### Nmap Port Scan

```bash
nmap -p- -sV -sC --open <target_ip>
```

### IIS ShortName Scanner (Java)

```bash
java -jar iis_shortname_scanner.jar 0 5 http://<target_ip>/
```

* Automates short-name detection.
* `0`: start position; `5`: max short name length.
* Requires **Java** installed.

### Wordlist Generation

```bash
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

Creates a **filtered wordlist** from all matching entries.

### Gobuster for Directory Discovery

```bash
gobuster dir -u http://<target_ip>/ -w /tmp/list.txt -x .asp,.aspx
```

Performs **brute-force directory enumeration** using custom extensions.

---

## 4. Example Output

```
200 OK - /SECRET~1
200 OK - /DOCUME~1/FILE.TXT
403 Forbidden - /ADMIN~1
```

* `/SECRET~1`: valid directory
* `/DOCUME~1/FILE.TXT`: accessible file
* `/ADMIN~1`: existing but restricted

---

## 5. Mitigation

### Disable 8.3 Name Generation

```powershell
fsutil behavior set disable8dot3 1
```

### Block Tilde Requests in IIS

* Use **URL Rewrite Rules** to reject any requests containing `~`.

### General Hardening

* Restrict directory listings.
* Review file system permissions.
* Regularly update IIS to patched versions.
