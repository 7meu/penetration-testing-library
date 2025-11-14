# ColdFusion Discovery and Enumeration

## 1. Overview

* **ColdFusion:** A Java-based web application platform using CFML (ColdFusion Markup Language).
* **Use Cases:** Dynamic content, database-driven applications, and web content management.
* **File Extensions:** `.cfm`, `.cfc`
* **Common Ports:** `80`, `443`, `1935`, `25`, `8500`, `5500`

---

## 2. Enumeration Techniques

### Port Scanning

ColdFusion commonly runs on **port 8500**.

```bash
nmap -p- -sC -Pn <target_ip> --open
```

### File Extensions

Look for `.cfm` and `.cfc` files in web directories.

### HTTP Header Analysis

```bash
curl -I http://<target_ip>
```

Headers may contain `Server: ColdFusion` or `X-Powered-By: ColdFusion`.

### Error Message Inspection

ColdFusion error pages often display stack traces or CFML tags revealing environment details.

### Default Files and Directories

Check for default administrative paths:

* `/CFIDE/administrator/`
* `/CFIDE/`
* `/cfdocs/`
* `/admin.cfm`

Presence of `/CFIDE/administrator` typically confirms a ColdFusion instance.

---

## 3. Enumeration Commands

### Full Port and Service Scan

```bash
nmap -p- -sC -Pn <target_ip> --open
```

### Manual Directory Browsing

Visit:

* `http://<target_ip>:8500`
* `https://<target_ip>:8500`
* `http://<target_ip>:8500/CFIDE/administrator`

### Header Retrieval

```bash
curl -I http://<target_ip>
```

### Gobuster Scan

```bash
gobuster dir -u http://<target_ip> -w <wordlist>
```

### Dirb Scan

```bash
dirb http://<target_ip> <wordlist>
```

### FFuf Scan

```bash
ffuf -w <wordlist> -u http://<target_ip>/FUZZ
```

