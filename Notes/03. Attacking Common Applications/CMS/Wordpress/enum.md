# Wordpress Discovery and Enumeration

## 1. Basic Identification

```bash
curl -s http://blog.inlanefreight.local/robots.txt
curl -s http://blog.inlanefreight.local/wp-admin/
curl -s http://blog.inlanefreight.local/wp-content/plugins/
curl -s http://blog.inlanefreight.local/wp-content/themes/
```

## 2. Version and Theme Enumeration

```bash
curl -s http://blog.inlanefreight.local | grep -i "WordPress"
curl -s http://blog.inlanefreight.local | grep -i "themes"
curl -s http://blog.inlanefreight.local/wp-content/themes/transport-gravity/style.css
curl -s http://blog.inlanefreight.local/wp-content/themes/transport-gravity/readme.txt
```

## 3. Plugin Enumeration

```bash
curl -s http://blog.inlanefreight.local | grep -i "plugins"
curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt
curl -s http://blog.inlanefreight.local/?p=1 | grep -i "plugins"
```

## 4. User Enumeration (Manual)

```bash
curl -s -I http://blog.inlanefreight.local/wp-login.php -d "log=admin&pwd=invalid"
curl -s -I http://blog.inlanefreight.local/wp-login.php -d "log=invalid&pwd=invalid"
```

## 5. WPScan Enumeration

```bash
wpscan --url http://blog.inlanefreight.local --enumerate --api-token YOUR_API_TOKEN
wpscan --url http://blog.inlanefreight.local --enumerate u,p,t
wpscan --url http://blog.inlanefreight.local --passwords /usr/share/wordlists/rockyou.txt --usernames admin,john
wpscan --url http://blog.inlanefreight.local --proxy 127.0.0.1:8080
```

## 6. XML-RPC Checks

```bash
curl -i http://blog.inlanefreight.local/xmlrpc.php
curl -X POST -d '<methodCall><methodName>system.listMethods</methodName></methodCall>' http://blog.inlanefreight.local/xmlrpc.php
```

## 7. Additional Security Checks

```bash
curl -s http://blog.inlanefreight.local/robots.txt | grep "Disallow"
curl -s http://blog.inlanefreight.local/wp-config.php
curl -s http://blog.inlanefreight.local | grep -Eo "(http|https)://[a-zA-Z0-9./?=_%:-]*"
curl -s http://blog.inlanefreight.local/wp-content/uploads/
```
