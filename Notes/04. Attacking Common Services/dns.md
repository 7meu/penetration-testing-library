# DNS

## 1. DNS Basics

* Translates domain names to IP addresses.
* Ports:
  * UDP/53
  * TCP/53 (zone transfers, large packets)
* Common attack vectors:
  * Zone transfers
  * Domain/subdomain takeovers
  * DNS spoofing/cache poisoning

## 2. Enumeration

```bash
nmap -p53 -Pn -sV -sC <target_IP>
```

## 3. DNS Zone Transfers

```bash
dig AXFR @<nameserver> <domain>
fierce --domain <domain>
```

## 4. Domain and Subdomain Takeovers

### Subdomain Enumeration

```bash
./subfinder -d <domain> -v
git clone https://github.com/TheRook/subbrute.git
cd subbrute
echo "<nameserver>" > resolvers.txt
./subbrute <domain> -s names.txt -r resolvers.txt
```

### CNAME Inspection

```bash
host <subdomain>
nslookup <subdomain>
```

Check for indicators such as “NoSuchBucket” (AWS S3) to verify takeover potential.

## 5. DNS Spoofing and Cache Poisoning

### MITM-Based Cache Poisoning (Example Workflow)

1. Edit `/etc/ettercap/etter.dns` with spoofed entries.
2. Start Ettercap, scan hosts.
3. Add victim IP as Target1 and gateway as Target2.
4. Enable the `dns_spoof` plugin.

Bettercap can be used similarly.

## 6. Recent DNS Vulnerabilities

* Forgotten CNAME records pointing to expired services can enable subdomain takeovers.
* Risks include phishing, cookie theft, CSRF, CORS bypass, and CSP bypass.
