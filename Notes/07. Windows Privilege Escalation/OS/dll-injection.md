# **DLL Injection**

## **Windows API Calls (Common Injection Flow)**

```powershell
OpenProcess          # Obtain handle to target process
VirtualAllocEx       # Allocate memory inside target
WriteProcessMemory   # Write DLL path or shellcode
GetProcAddress       # Get address of LoadLibrary or target export
CreateRemoteThread   # Execute LoadLibrary inside target
LoadLibrary          # Load DLL into process
FreeLibrary          # Unload DLL
```

---

# **DLL Hijacking (Registry)**

```powershell
regedit
```

*(Used to adjust DLL search order or modify paths.)*

---

# **DLL Hijacking (Process Monitoring)**

```powershell
procmon  # Process Monitor (Sysinternals)
```

*(Identify missing DLLs - place malicious DLL with same name.)*

---

# **Key Concepts**

### **DLL Injection**

* Inject a DLL into a running process to execute arbitrary code.
* Uses WinAPI (LoadLibrary-based), manual mapping, or reflective loading.

### **LoadLibrary Injection**

* Classic technique using `CreateRemoteThread` to call `LoadLibrary`.
* Leaves artifacts (DLL path on disk & memory).

### **Manual Mapping**

* Map DLL sections manually (no LoadLibrary).
* Harder to detect; bypasses traditional monitoring.

### **Reflective DLL Injection**

* DLL contains its own loader (`ReflectiveLoader`).
* Loads itself from memory without touching disk.

### **DLL Hijacking**

* Abuse Windows DLL search order.
* Drop malicious DLL where process looks first.

### **DLL Proxying**

* Create DLL with same exports.
* Forward calls to real DLL.
* Intercept/modify specific functions.

---

# **Approach, Commands, Tools, and Techniques**

### **1. LoadLibrary-Based Injection**

* Use WinAPI sequence:

  * `OpenProcess`
  * `VirtualAllocEx`
  * `WriteProcessMemory`
  * `GetProcAddress(LoadLibraryA)`
  * `CreateRemoteThread`

### **2. Manual Mapping**

* Map PE headers, sections.
* Resolve imports.
* Apply relocations.
* Execute DLL entry point manually.

### **3. Reflective DLL Injection**

* Use code like Stephen Fewer’s ReflectiveLoader.
* Load DLL from memory buffer.

### **4. DLL Hijacking**

* Use **ProcMon** to find missing DLL lookups.
* Drop malicious DLL in searched path.
* Modify search order via `SafeDllSearchMode` in registry.

### **5. DLL Proxying**

* Write proxy DLL with identical exports.
* Load original DLL manually.
* Hook desired exported functions.

---

# **Commands**

* `regedit`
* `procmon`
* Windows API calls:

  * `OpenProcess`, `VirtualAllocEx`, `WriteProcessMemory`
  * `GetProcAddress`, `CreateRemoteThread`
  * `LoadLibrary`, `FreeLibrary`

---

# **Tools**

* Process Monitor (Sysinternals)
* Process Explorer
* PE Explorer / CFF Explorer
* C/C++ compilers (cl.exe, mingw)
* Debuggers (WinDbg, x64dbg)

---

# **Techniques**

* Building malicious DLLs (export matching)
* Reverse engineering DLL dependencies
* Monitoring file access for missing DLLs
* In-memory DLL loading methods
