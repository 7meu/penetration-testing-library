# Credentialed Enumeration - from Linux

### 1. CrackMapExec (CME)

#### Function
* Enumerates users, groups, logged-on users, and SMB shares.
* Supports share spidering.

#### Commands
```bash
crackmapexec smb <target_ip> -u <username> -p <password> --users
crackmapexec smb <target_ip> -u <username> -p <password> --groups
crackmapexec smb <target_ip> -u <username> -p <password> --loggedon-users
crackmapexec smb <target_ip> -u <username> -p <password> --shares
crackmapexec smb <target_ip> -u <username> -p <password> -M spider_plus --share '<share_name>'
```

### 2. SMBMap

* Enumerates SMB shares, permissions, and file structures.

```bash
smbmap -u <username> -p <password> -d <domain> -H <target_ip>
smbmap -u <username> -p <password> -d <domain> -H <target_ip> -R '<share_name>' --dir-only
```

### 3. rpcclient

* MS-RPC interaction for AD enumeration.

```bash
rpcclient -U "" -N <target_ip>
rpcclient $> enumdomusers
rpcclient $> queryuser <rid>
```

### 4. Impacket

* Toolkit for Windows protocol interaction.

```bash
psexec.py <domain>/<username>:'<password>'@<target_ip>
wmiexec.py <domain>/<username>:'<password>'@<target_ip>
```

### 5. Windapsearch

* LDAP-based enumeration of users, groups, and GPOs.

```bash
windapsearch.py -d <domain> --dc-ip <dc_ip> -u <username> -p <password> --users
```
