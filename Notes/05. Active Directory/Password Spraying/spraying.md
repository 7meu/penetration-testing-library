# Password Spraying - Making a Target User List

## Enumerating Password Policies

### CrackMapExec (CME)

```bash
crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```

---

## Enumerating Password Policies Without Credentials (Linux)

### rpcclient

```bash
rpcclient -U "" -N 172.16.5.5
```

Useful commands:

```
querydominfo
getdompwinfo
```

### enum4linux

```bash
enum4linux -P 172.16.5.5
```

### enum4linux-ng

```bash
enum4linux-ng -P 172.16.5.5 -oA ilfreight
cat ilfreight.json
```

### ldapsearch (Anonymous Bind)

```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

---

## Enumerating Password Policies from Windows

### SMB NULL Session

```powershell
net use \\DC01\ipc$ "" /u:""
```

### Authenticated Access

```powershell
net accounts
```

### PowerView

```powershell
Import-Module .\PowerView.ps1
Get-DomainPolicy
```

---

## User Enumeration (Building a User List)

### enum4linux

```bash
enum4linux -U 172.16.5.5 | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
```

### rpcclient

```bash
rpcclient -U "" -N 172.16.5.5
```

Useful command:

```
enumdomusers
```

### CrackMapExec

```bash
crackmapexec smb 172.16.5.5 --users
crackmapexec smb 172.16.5.5 -u htb-student -p Academy_student_AD! --users
```

### ldapsearch

```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))" | grep sAMAccountName: | cut -f2 -d" "
```

### windapsearch

```bash
./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
```

### Kerbrute

```bash
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt
```

---

## Critical Password Policy Parameters

* Minimum password length
* Lockout threshold
* Lockout duration
* Password complexity

---

## Password Spraying Considerations

* Always determine password policy before spraying.
* Use minimal attempts to avoid lockouts.
* Create an accurate, deduplicated user list.
* When unsure of policy, increase time between attempts.

---

## User Enumeration Techniques

### SMB NULL Sessions

* May expose full domain user list.
* Tools: rpcclient, enum4linux, CrackMapExec.

### LDAP Anonymous Binds

* Useful when domain allows anonymous LDAP queries.
* Tools: ldapsearch, windapsearch.

### Kerbrute

* Efficient user validation via Kerberos.

### Credentialed Enumeration

* Tools: CME, PowerView.

### OSINT

* Tools: linkedin2username.

---

## Additional Notes

* Kerbrute user enumeration triggers event ID **4768**.
* CME `--users` output shows `badpwdcount` and `baddpwdtime`.
* Bad password counts may differ across domain controllers.
* The PDC emulator holds the most accurate bad password count.
* Always maintain logs of attempts, users, and timestamps.
