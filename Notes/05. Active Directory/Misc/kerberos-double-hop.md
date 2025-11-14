# Kerberos Double Hop Problem

## Overview

The Kerberos double-hop problem occurs when a user authenticates to a remote system using Kerberos and then attempts to authenticate to a second system from that remote session. Because Kerberos does not automatically forward user credentials to the next hop, authentication to the third system fails. This commonly affects PowerShell Remoting, WinRM, and multi-tier architectures.

---

## Steps Demonstrating the Double Hop Issue

### 1. Establish a WinRM Session

```powershell
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm
```

### 2. Change Directory

```powershell
cd 'C:\Users\Public\'
```

### 3. View Credentials with Mimikatz (Requires Privileged Access)

```powershell
.\mimikatz "privilege::debug" "sekurlsa::logonpasswords" exit
```

### 4. List Processes for a Specific User

```powershell
tasklist /V | findstr backupadm
```

### 5. Check Kerberos Tickets

```powershell
klist
```

### 6. Import PowerView

```powershell
Import-Module .\PowerView.ps1
```

### 7. Enumerate Domain Users with SPNs

```powershell
Get-DomainUser -SPN
```

### 8. Create Credential Object

```powershell
$SecPassword = ConvertTo-SecureString '!qazXSW@' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\backupadm', $SecPassword)
```

### 9. Query SPN Users with Explicit Credentials

```powershell
Get-DomainUser -SPN -Credential $Cred | select samaccountname
```

### 10. Register New PSSession Configuration

```powershell
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential INLANEFREIGHT\backupadm
```

### 11. Restart WinRM

```powershell
Restart-Service WinRM
```

### 12. Enter PSSession with the Configuration

```powershell
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm -ConfigurationName backupadmsess
```

---

## Solution: Enable CredSSP Authentication

CredSSP allows explicit delegation of user credentials to the remote host, resolving the double-hop problem.

### Enable CredSSP on Client

```powershell
Enable-WSManCredSSP -Role Client -DelegateComputer DEV01
```

### Enable CredSSP on Server (DEV01)

```powershell
Enable-WSManCredSSP -Role Server
```

### Use PowerShell Remoting with CredSSP

```powershell
Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm -Authentication CredSSP
```

### Verify CredSSP Status

```powershell
Get-WSManCredSSP
```

---

## Alternative: Kerberos Constrained Delegation (KCD)

Kerberos Constrained Delegation can forward credentials without using CredSSP. This requires configuring permitted delegation targets in Active Directory and is often used in secure enterprise setups.
