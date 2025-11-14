# Domain Trusts Primer

## Domain Trusts Overview

Trusts establish authentication paths between domains or forests, allowing users to access resources outside their own domain.

### Types of Trusts

- **Parent-child** - Within the same forest, transitive and bidirectional  
- **Cross-link** - Between child domains, transitive  
- **External** - Between separate forests, non-transitive, uses SID filtering  
- **Tree-root** - Between forest root and a new tree, transitive and bidirectional  
- **Forest** - Between forest root domains, transitive  
- **ESAE** - Used for hardened administrative (bastion) forests  

### Trust Attributes

- **Transitive / Non-transitive**  
  - Transitive trusts extend trust to other trusted objects  
  - Non-transitive trusts trust only the direct domain  

- **One-way / Bidirectional**  
  - One-way: users in the trusted domain can access resources in the trusting domain  
  - Bidirectional: trust flows both ways  

---

## Enumerating Trust Relationships

### Get-ADTrust (PowerShell)

```powershell
Import-Module activedirectory
Get-ADTrust -Filter *
```

### PowerView

```powershell
Get-DomainTrust
Get-DomainTrustMapping
Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName
```

### netdom

```cmd
netdom query /domain:inlanefreight.local trust
netdom query /domain:inlanefreight.local dc
netdom query /domain:inlanefreight.local workstation
```

### BloodHound

Use the **Map Domain Trusts** query to visualize trust paths.

---

## Key Points

* Trusts can introduce security weaknesses when misconfigured.
* Understanding trust direction and scope is essential for both attack and defense.
* Enumeration is necessary to assess exposure.
* Testing trust relationships must always follow the rules of engagement.
* Trusts can provide attackers with effective pivot paths between domains.
