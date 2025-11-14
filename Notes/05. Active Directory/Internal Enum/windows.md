# Credentialed Enumeration - from Windows

## 1. Active Directory PowerShell Module

**Function:** Administers Active Directory from the command line.

**Key Cmdlets:**
* `Get-Module`
* `Import-Module ActiveDirectory`
* `Get-ADDomain`
* `Get-ADUser`
* `Get-ADTrust`
* `Get-ADGroup`
* `Get-ADGroupMember`

**Commands:**
```powershell
Get-Module
Import-Module ActiveDirectory
Get-ADDomain
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
Get-ADTrust -Filter *
Get-ADGroup -Filter * | select name
Get-ADGroup -Identity "Backup Operators"
Get-ADGroupMember -Identity "Backup Operators"
```

## 2. PowerView / SharpView

* `Get-DomainUser`
* `Get-DomainGroupMember`
* `Get-DomainTrustMapping`
* `Test-AdminAccess`
* `Get-DomainGPO`
* `Get-DomainUser -SPN`
* `Get-DomainUser -TrustedToAuth`

**PowerView Commands:**

```powershell
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
Get-DomainTrustMapping
Test-AdminAccess -ComputerName ACADEMY-EA-MS01
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
Get-DomainGPO
Get-DomainUser -TrustedToAuth
```

**SharpView Commands:**

```powershell
.\SharpView.exe Get-DomainUser -Identity forend
.\SharpView.exe Get-DomainUser -Help
```

## 3. Snaffler

```powershell
Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data
Snaffler.exe -s \\fileserver\shared -o snaffler.log -v data
```

## Additional Commands

```powershell
Get-Help Get-ADUser -Full
Get-Command -Module ActiveDirectory
Set-ExecutionPolicy Bypass -Scope Process
Import-Module .\PowerView.ps1
Select-String -Path snaffler.log -Pattern "password|creds|admin"
Get-ADReplicationMetadata -Object "CN=Administrator,CN=Users,DC=domain,DC=com" -Server DC01
```
