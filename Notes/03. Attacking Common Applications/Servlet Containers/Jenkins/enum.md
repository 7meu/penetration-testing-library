# Enumerating Jenkins

## 1. Jenkins Version Detection

### HTTP Headers

```bash
curl -I http://<target>:8080/
```

### Nmap Service Detection

```bash
nmap -sV -p 8080 <target_ip>
```

### Jenkins CLI Detection

```bash
curl http://<target>:8080/jenkins/cli/ | grep -i "Jenkins CLI"
```

These methods reveal version and environment details useful for identifying known exploits.

---

## 2. Jenkins Port Enumeration

| Port | Purpose                   |
| ---- | ------------------------- |
| 8080 | Web Interface (HTTP)      |
| 5000 | Slave/Agent Communication |

```bash
nmap -sV -p 8080,5000 <target_ip>
```

---

## 3. Jenkins CLI Enumeration

The Jenkins CLI provides administrative functionality that can often be accessed remotely.

**Help Menu:**

```bash
java -jar jenkins-cli.jar -s http://<target>:8080/jenkins/ help
```

**List Installed Plugins:**

```bash
java -jar jenkins-cli.jar -s http://<target>:8080/jenkins/ list-plugins
```

**List Jobs:**

```bash
java -jar jenkins-cli.jar -s http://<target>:8080/jenkins/ list-jobs
```

Enumerating plugins and jobs can reveal misconfigurations, sensitive scripts, or exploitable components.

---

## 4. Plugin Enumeration

**Access the Plugin Manager:**

```bash
curl http://<target>:8080/jenkins/pluginManager/
```

* Plugins are a major attack surface.
* Identify version numbers and search for matching CVEs (e.g., on NVD or Exploit-DB).
* Example exploit: **Pipeline: Groovy Plugin (CVE-2019-10352)** - Arbitrary code execution through script injection.

---

## 5. Security Realm Enumeration

Determine which authentication method is used.

```bash
curl http://<target>:8080/jenkins/configureSecurity/
```

Possible realms:
* Jenkins internal database
* LDAP integration
* Active Directory or external OAuth

Misconfigured realms may allow unauthorized access or user enumeration.

---

## 6. API Enumeration

### Basic API Endpoints

```bash
curl http://<target>:8080/jenkins/api/
curl http://<target>:8080/jenkins/api/json
```

### API Tokens

* Look for exposed tokens in `/api/json` responses or configuration backups.
* Tokens can allow authenticated actions without a password.

### Fuzzing and Testing

* Enumerate API endpoints for possible parameter manipulation or command injection.
* Tools such as `ffuf`, `burp`, or custom Python scripts can help identify exploitable parameters.

---

## 7. Access Control Review

Check configuration of user roles and authorization settings:

```bash
curl http://<target>:8080/jenkins/configureSecurity/
```

Look for:
* Anonymous read or job build permissions
* Misconfigured “Matrix-based Security” allowing privilege escalation
* Open script consoles or build permissions for untrusted users
