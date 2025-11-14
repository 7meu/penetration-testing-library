# Active Directory Basic Commands

#### General Commands

```powershell
Get-Module
Get-Command -Module ActiveDirectory
Get-Help <cmd-let>
Import-Module ActiveDirectory
```

### Active Directory PowerShell Commands

#### AD User Commands

```powershell
New-ADUser -Name "first last" -Accountpassword (Read-Host -AsSecureString "Super$ecurePassword!") -Enabled $true -OtherAttributes @{title="Analyst";mail="f.last@domain.com"}
Remove-ADUser -Identity <name>
Unlock-ADAccount -Identity <name>
Set-ADAccountPassword -Identity <name> -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "NewP@ssw0rdReset!" -Force)
Set-ADUser -Identity amasters -ChangePasswordAtLogon $true
```

#### AD Group Commands

```powershell
New-ADOrganizationalUnit -Name "name" -Path "OU=folder,DC=domain,DC=local"
New-ADGroup -Name "name" -SamAccountName analysts -GroupCategory Security -GroupScope Global -DisplayName "Security Analysts" -Path "CN=Users,DC=domain,DC=local" -Description "Members of this group are Security Analysts under the IT OU"
Add-ADGroupMember -Identity 'group name' -Members 'ACepheus,OStarchaser,ACallisto'
```

#### GPO Commands

```powershell
Copy-GPO -SourceName "GPO to copy" -TargetName "Name"
New-GPLink -Name "Security Analysts Control" -Target "ou=Security Analysts,ou=IT,OU=HQ-NYC,OU=Employees,OU=Corp,dc=INLANEFREIGHT,dc=LOCAL" -LinkEnabled Yes
Set-GPLink -Name "Security Analysts Control" -Target "ou=Security Analysts,ou=IT,OU=HQ-NYC,OU=Employees,OU=Corp,dc=INLANEFREIGHT,dc=LOCAL" -LinkEnabled Yes
```

#### Computer Commands

```powershell
Add-Computer -DomainName 'INLANEFREIGHT.LOCAL' -Credential 'INLANEFREIGHT\HTB-student_adm' -Restart
Add-Computer -ComputerName 'name' -LocalCredential '.\localuser' -DomainName 'INLANEFREIGHT.LOCAL' -Credential 'INLANEFREIGHT\htb-student_adm' -Restart
Get-ADComputer -Identity "name" -Properties * | Select CN,CanonicalName,IPv4Address
Get-ADGroup -Identity "Server Operators" -Properties *
Get-ADGroup -Identity "Domain Admins" -Properties * | Select DistinguishedName,GroupCategory,GroupScope,Name,Members
```
