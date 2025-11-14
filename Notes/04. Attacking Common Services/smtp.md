# SMTP

## 1. Email Basics

* SMTP handles sending email.
* POP3 and IMAP4 retrieve email.
* Ports:
  * SMTP: 25, 465, 587
  * POP3: 110, 995
  * IMAP: 143, 993
* Common issues include user enumeration, weak authentication, open relay, and credential reuse.

## 2. Enumeration

### MX Records

```bash
host -t MX <domain>
dig mx <domain>
```

### A Records

```bash
host -t A <mail_server>
dig A <mail_server>
```

### SPF, DKIM, DMARC

```bash
dig txt <domain>
```

### Nmap

```bash
nmap -Pn -sV -sC -p25,143,110,465,587,993,995 <target_IP>
```

## 3. Misconfigurations

### SMTP User Enumeration

```bash
telnet <target_IP> 25
VRFY <username>
EXPN <mailing_list>
RCPT TO:<username>
```

### smtp-user-enum

```bash
smtp-user-enum -M VRFY -U <user_list> -D <domain> -t <target_IP>
```

### POP3 User Check

```bash
telnet <target_IP> 110
USER <username>
PASS <password>
```

## 4. Cloud Enumeration

### Office 365

```bash
python3 o365spray.py --validate --domain <domain>
python3 o365spray.py --enum -U <user_list> --domain <domain>
```

### Google Workspace

```bash
python3 enum_google.py --domain <domain>
```

## 5. Password Attacks

### Hydra

```bash
hydra -L <user_list> -P <password_list> <target_IP> <service>
```

### Office 365 Spray

```bash
python3 o365spray.py --spray -U <user_list> -p <password> --count 1 --lockout 1 --domain <domain>
```

### MailSniper

```powershell
Invoke-Spray -Usernames <user_list> -Password <password> -OutFile results.txt
```

## 6. Protocol-Specific Attacks

### Open Relay Testing

```bash
nmap -p25 -Pn --script smtp-open-relay <target_IP>
swaks --from <sender> --to <recipient> --header "Subject: Test" --body "Test" --server <target_IP>
```

### SMTP Spoofing (Manual)

```bash
telnet <target_IP> 25
HELO example.com
MAIL FROM:<spoof@domain.com>
RCPT TO:<victim@domain.com>
DATA
Subject: Test
Spoofing test.
.
QUIT
```

## 7. Recent Vulnerabilities

* **CVE-2020-7247 (OpenSMTPD):** RCE, unauthenticated.
* **CVE-2021-27211 (Exim):** Improper recipient validation leading to RCE.

## 8. Additional Tools

* Amass
* Shodan
* TheHarvester
* Censys
