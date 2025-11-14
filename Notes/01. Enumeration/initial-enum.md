# Initial Enumeration

## 1. Scanning

### 1.1 Nmap TCP Quick

```bash
sudo nmap -Pn -v -sS -sV -sC -oN tcp-quick.nmap IP
```

### 1.2 Nmap TCP Full

```bash
sudo nmap -Pn -sS --stats-every 3m --max-retries 1 --max-scan-delay 20 --defeat-rst-ratelimit -T4 -p1-65535 -oN tcp-full.nmap -sV IP 
```

### 1.3 Nmap TCP (Repeat if extra ports found)

```bash
sudo nmap -Pn -v -sS -A -oN tcp-extra.nmap -p PORTS IP 
```

### 1.4 Nmap UDP Quick

```bash
sudo nmap -Pn -v -sU -sV --top-ports=30 -oN udp-quick.nmap IP
```

### 1.5 Nmap UDP 1000

```bash
sudo nmap -Pn --top-ports 1000 -sU --stats-every 3m --max-retries 1 -T4 -oN udp-1000.nmap IP
```

### 1.6 Nmap UDP (Repeat if extra ports found)

```bash
sudo nmap -Pn -sU -A -oN udp-extra.nmap -p PORTS IP 
```

### 1.7 ICMP Sweep

```bash
fping -a -g 10.10.10.0/24 2>/dev/null
```

### 1.8 ARP Scan (Local Network)

```bash
arp-scan -l
```

## 2. Enumeration

### 2.1 FTP (port 21)

* Check for version vulnerabilities, anonymous login, read/write access, and web root exposure.

### 2.2 SSH (port 22)

```bash
nmap -sV --script=ssh-hostkey -p22 IP
```

* Check version vulnerabilities, user enumeration, password login, and host key reuse.
* Use tools like CeWL, Hydra, Patator, Crowbar, or MSF for brute-forcing if necessary.
* If ports get filtered - fail2ban

### 2.3 Telnet (port 23)

* Connect and check for service information.

### 2.4 SMTP (port 25)

* Check version with `HELO` or `HELLO <domain>` and identify vulnerabilities.

### 2.5 POP (port 110)

```bash
user <username> 
pass <password> 
LIST
RETR <email number>
```

### 2.6 DNS (port 53)

* Check for zone transfers or domain controller indications.

### 2.7 Kerberos (port 88)

```bash
kerbrute userenum --dc IP -d DOMAIN users.txt
GetNPUsers.py domain.local/ -usersfile users.txt -no-pass
```

### 2.8 NetBIOS (port 139)

```bash
nmblookup -A IP
nbtscan IP 
```

* For older hosts, SMB/SAMBA may run here, scan by adding 'client min protocol = LANMAN1' to GLOBAL setting in /etc/samba/smb.conf or by using --option='client min protocol'=LANMAN1 with smbclient

### 2.9 RPC (port 135)

```bash
sudo nmap -sS -Pn -sV --script=rpcinfo.nse -p135 IP
rpcinfo IP
rpcclient -U "" -N IP
```

### 2.10 LDAP (ports 389, 636, 3268, 3269)

```bash
sudo nmap -sS -Pn -sV --script=ldap* -p389,636,3268,3269 IP
```

### 2.11 SNMP (port 161)

```bash
snmpwalk -v2c -c public IP
snmp-check IP
onesixtyone -c community.txt IP
sudo nmap -sU -sV -p161 --script snmp* IP
snmpenum -t IP -c public
```

### 2.12 Oracle (port 1521)

```bash
tnscmd10g version -h IP
nmap -sV --script=oracle-tns-version -p1521 IP
odat tnscmd -s IP --ping
odat all -s IP -p1521
odat sidguesser -s IP
```

### 2.13 MySQL (port 3306)

```bash
mysql -h IP -u root -p
nmap -sV --script=mysql* -p3306 IP
hydra -L users.txt -P passwords.txt mysql://IP
```

### 2.14 Web (ports 80/443)

```bash
sudo nmap -Pn -sC -p80,443 IP
```

* Check for web server vulnerabilities, CGI shellshock, certificates, robots.txt, sitemap.xml, and default credentials.
* Inspect source code, frameworks, and for LFI/RFI/SQLi/Command Injection.

#### Dirb

```bash
dirb IP
dirb IP -X .php,.asp,.txt,.jsp
dirb IP -a 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'
```

#### Gobuster

```bash
gobuster dir --url IP --wordlist /usr/share/seclists/Discovery/Web-Content/big.txt
gobuster dir --url IP --wordlist /usr/share/seclists/Discovery/Web-Content/big.txt -k -a 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'
```

#### Nikto

```bash
nikto -host IP
```

#### WhatWeb / Wappalyzer

```bash
whatweb http://IP
wappalyzer http://IP
```

#### WPScan (WordPress)

```bash
wpscan --url http://IP --enumerate u
```

### 2.15 SMB (ports 139, 445)

#### Enumeration

```bash
sudo nmap -Pn --script=smb-proto* -p139,445 
sudo nmap -Pn --script=smb-os-discovery.nse -p139,445
sudo nmap -Pn --script=smb-enum* -p139,445
sudo nmap -Pn --script=smb-vuln* -p139,445
nmap -p445 -vv --script=smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-ms17-010.nse IP
crackmapexec smb IP -u '' -p '' --shares
```

#### Null Sessions

```bash
nmap --script smb-enum-shares -p139,445 IP
smbclient -L \\IP\ -N 
smbclient -m=SMB2 -L \\Hostname\ -N
```

#### Share Access

```bash
smbclient \\IP\Share -N 
smbmap -H IP
enum4linux -a IP
```

#### Impacket

```bash
impacket-smbclient -no-pass IP
impacket-lookupsid domain/username:password@IP
```

#### Permissions

```bash
smb: \> showacls
smb: \> dir
```

#### Mounting

```bash
sudo mount -t cifs //IP/SHARE ~/mount_directory
```

#### Listing Files

```bash
smbmap -R -H IP
smbmap -R Replication -H IP
```

#### Recursive Download

```bash
smbclient //<IP>/Replication
smb: \> recurse ON
smb: \> prompt OFF
smb: \> mget *
```

#### Upload/Download Files

```bash
smbmap -H IP --download 'Replication\file'
smbmap -H IP --upload test.txt SHARE/test.txt 
```

### 2.16 NFS (port 2049)

```bash
showmount -e IP 
mount -t nfs -o vers=3 IP:/home ~/home
mount -t nfs4 -o proto=tcp,port=2049 127.0.0.1:/srv/share mountpoint
```

### 2.17 TFTP (UDP 69)

* Use `tftp` or `atftp` clients to connect and retrieve files.

## 3. Automation Tools

### 3.1 AutoRecon

```bash
autorecon IP
```

### 3.2 NmapAutomator

```bash
./NmapAutomator.sh IP All
```

## 4. Finding Exploits

```bash
searchsploit apache 2.4.49
searchsploit -x path/to/exploit
```

* Check ExploitDB, CVE databases, and perform targeted searches using service banners.
