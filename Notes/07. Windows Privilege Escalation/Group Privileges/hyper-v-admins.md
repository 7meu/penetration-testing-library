# Hyper-V Administrators

```powershell
takeown /F "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"
sc.exe start MozillaMaintenance
```

## Key Points

### Virtualization Access

* Members of **Hyper-V Administrators** can fully control virtual machines.
* They can clone a Domain Controller, mount the VHD/VHDX, and extract **NTDS.dit** and registry hives.
* This provides full domain credential extraction.

### Hard-Link Exploit (Legacy)

* Deleting a `.vhdx` triggers `vmms.exe` (SYSTEM) to restore permissions on the path.
* This allowed creation of hard links to protected files.
* Replacing a linked file and starting its service resulted in **SYSTEM** execution.
* Often abused with services like **Mozilla Maintenance Service**.
* Patched as of **March 2020**.

### Mozilla Maintenance Service Example

* Create ownership over the service executable.
* Replace with malicious binary.
* Start the service for SYSTEM-level execution.

---

## Approach, Commands, Tools, Techniques

### 1. Virtualization Access

**Technique:**

* Export/clone a virtual Domain Controller.
* Mount the virtual disk offline.
* Extract:

  * `NTDS.dit`
  * `SYSTEM` and `SECURITY` hives

**Tools:**

* Hyper-V Manager
* Disk Management / `mount-vhd`
* `secretsdump.py` / DSInternals

**Outcome:**

* Full domain credential extraction.

---

### 2. Hard-Link Exploit (Deprecated / Patched)

**Technique:**

* Delete `.vhdx` - create hard link to protected file.
* Replace executable.
* Start service - SYSTEM shell.

**Tools:**

* PowerShell hard-link creation
* `takeown`
* `sc.exe`

**Commands:**

```powershell
takeown /F "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"
sc.exe start MozillaMaintenance
```

**Outcome:**

* SYSTEM code execution (only on unpatched systems).

---

## Mitigation

* Hard-link exploit requires vulnerable services or pre-2020 patch levels.
* Hyper-V Admins effectively have near-Domain-Admin power due to VM control.
* Segregate virtualization privileges.
* Monitor for unusual VM exports, checkpoints, and disk mounts.
* Keep virtualization hosts fully patched.
