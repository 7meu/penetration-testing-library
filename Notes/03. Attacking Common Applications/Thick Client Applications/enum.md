# Exploiting Web Vulnerabilities in Thick Client Applications

## 1. Overview
* Thick client applications communicate with backend servers and often expose attack surfaces through insecure local logic or weak client-server communication.
* Common risks include:

  * Path traversal
  * SQL injection
  * Unencrypted traffic
  * Hardcoded credentials
  * Weak authentication
* Key attack vectors:
  * Client-side code analysis
  * Network interception
  * Abusing server-side logic flaws

---

## 2. Penetration Testing Steps

### 2.1 Initial Enumeration

* Identify application components, configuration files, logs, and communication methods.
* Determine:
  * Server OS
  * Database type
  * Programming language
* Common files to inspect:
  * JAR archives
  * XML config files (`beans.xml`, etc.)

### 2.2 Network Traffic Analysis

* Capture and inspect client-server communication.
* Extract:
  * API endpoints
  * Authentication tokens
  * Sensitive data

```bash
sudo tcpdump -i eth0 -w capture.pcap
sudo tshark -r capture.pcap -T fields -e http.request.uri
```

Tools: Wireshark, tcpdump, Burp Suite, mitmproxy

---

### 2.3 Client-Side Code Analysis

* Reverse-engineer the client to uncover:
  * SQL queries
  * Hidden logic
  * Hardcoded secrets
  * Path handling vulnerabilities

```bash
jar -xvf fatty-client.jar
jd-gui fatty-client.jar
```

Tools: JD-GUI, JADX, Ghidra, dnSpy

---

### 2.4 Path Traversal Exploitation

* Test file access mechanisms for improper validation.

Payload examples:

```
../../../etc/passwd
../../../../windows/system32/drivers/etc/hosts
```

---

### 2.5 SQL Injection

* Analyze queries found in client code.
* Inject malicious SQL payloads to bypass authentication or extract data.

Examples:
```sql
' OR '1'='1' --
' UNION SELECT username, password FROM users --
```

---

### 2.6 Reverse Engineering & Debugging

* Extract stored secrets, investigate logic flow, and uncover additional flaws.

```bash
strings restart-service.exe
x64dbg restart-service.exe
```

Tools: x64dbg, GDB, IDA Pro, Frida

---

### 2.7 Host File Manipulation (If Required)

Redirect client traffic for testing:

```bash
# Linux/macOS
echo "192.168.1.100 server.local" | sudo tee -a /etc/hosts

# Windows
echo 192.168.1.100 server.local >> C:\Windows\System32\drivers\etc\hosts
```
