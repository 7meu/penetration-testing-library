# 1. Situational Awareness

### Windows Network Information

```powershell
ipconfig /all
arp -a
route print
```

### Windows Protections Enumeration

```powershell
Get-MpComputerStatus
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
Get-AppLockerPolicy -Local | select -ExpandProperty RuleCollections
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

### General Enumeration

```powershell
tasklist
```

```bash
ps aux
```

```powershell
dir /s C:\
```

```bash
find / -type f -print
```

---

# 2. Initial Enumeration

### System Information

```powershell
systeminfo
ver
wmic qfe
wmic product get name
tasklist /svc
driverquery
```

### Network Information

```powershell
ipconfig /all
netstat -ano
arp -a
route print
nslookup
```

### User and Group Information

```powershell
whoami /all
whoami /priv
whoami /groups
net user
net user <username>
net localgroup
net localgroup <groupname>
query user
net accounts
```

### File System & Environment

```powershell
dir
type
set
echo %PATH%
icacls
reg query
```

### Scheduled Tasks

```powershell
schtasks /query /fo LIST /v
```

### PowerShell Equivalents

```powershell
Get-HotFix
Get-WmiObject Win32_Product | Select-Object Name, Version
Get-Service
Get-Process
Get-LocalUser
Get-LocalGroup
Get-ItemProperty
Get-ChildItem
Get-Acl
Get-ScheduledTask
```

---

# 3. Communication With Processes

### Network Enumeration

```powershell
netstat -ano
Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}
Get-NetUDPEndpoint
```

### Process Enumeration

```powershell
tasklist /svc
Get-Process
Get-Process | Where-Object {$_.UserName -like "*SYSTEM*"}
```

### Named Pipes

```powershell
pipelist.exe /accepteula
gci \\.\pipe\
accesschk.exe /accepteula \\.\Pipe\<pipename> -v
accesschk.exe /accepteula \pipe\* -v
accesschk.exe -w \pipe\* -v
accesschk.exe -accepteula -w \pipe\<pipename> -v
```

### Service Enumeration

```powershell
Get-Service
Get-Service | Select-Object Name, StartType, Status
```
