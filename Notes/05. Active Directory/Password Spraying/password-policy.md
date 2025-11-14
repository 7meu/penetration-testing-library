# Enumerating & Retrieving Password Policies

## CrackMapExec (CME)

```bash
crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```

Retrieves password policy details such as minimum length, complexity, and lockout thresholds.

---

## Enumerating Password Policies Without Credentials (Linux)

### SMB NULL Sessions

Older domain controllers may allow anonymous access.

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

### Relevant Ports

* 137/UDP - nmblookup, nbtstat
* 139/TCP, 135/TCP - net, RPC
* 445/TCP - smbclient
* 135/TCP - rpcclient

---

## LDAP Anonymous Binds

Legacy configurations may expose password policy via LDAP.

### ldapsearch

```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

---

## Enumerating Password Policies from Windows

### SMB NULL Sessions

```powershell
net use \\DC01\ipc$ "" /u:""
```

### Authenticated Access

#### net.exe

```powershell
net accounts
```

#### PowerView

```powershell
Import-Module .\PowerView.ps1
Get-DomainPolicy
```

---

## Key Parameters to Analyze

* Minimum password length
* Password history
* Maximum/minimum age
* Lockout threshold
* Lockout duration
* Complexity requirements

### Default Domain Settings

* Enforce history: 24
* Max age: 42 days
* Min age: 1 day
* Minimum length: 7
* Complexity: Enabled
* Lockout threshold: 0

---

## Password Spraying Considerations

* Always check policy first.
* Avoid lockouts by limiting attempts.
* Include delays between sprays.
* Create a precise target user list.
