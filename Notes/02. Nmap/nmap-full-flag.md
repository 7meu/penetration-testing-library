# Nmap Full Flag

## 1. Host Discovery

```bash
-sL    nmap 192.168.1.1-3 -sL                       # List targets only, no scan
-sn    nmap 192.168.1.1/24 -sn                      # Disable port scanning
-Pn    nmap 192.168.1.1-5 -Pn                       # Disable host discovery, port scan only
-PS    nmap 192.168.1.1-5 -PS22-25,80               # TCP SYN discovery on specified ports
-PA    nmap 192.168.1.1-5 -PA22-25,80               # TCP ACK discovery on specified ports
-PU    nmap 192.168.1.1-5 -PU53                     # UDP discovery on port 53
-PR    nmap 192.168.1.0/24 -PR                      # ARP discovery on local network
-n     nmap 192.168.1.1 -n                          # Disable DNS resolution
```

## 2. Target Specification

```bash
nmap 192.168.1.1                                    # Scan a single IP
nmap 192.168.1.1 192.168.2.1                        # Scan multiple IPs
nmap 192.168.1.1-254                                # Scan a range
nmap scanme.nmap.org                                # Scan a domain
nmap 192.168.1.0/24                                 # Scan with CIDR
-iL targets.txt                                     # Scan from file
-iR 100                                             # Scan 100 random hosts
--exclude 192.168.1.1                               # Exclude specific hosts
```

## 3. Scan Techniques

```bash
-sS     nmap 192.168.1.1 -sS                        # TCP SYN scan (default)
-sT     nmap 192.168.1.1 -sT                        # TCP connect scan
-sU     nmap 192.168.1.1 -sU                        # UDP scan
-sA     nmap 192.168.1.1 -sA                        # TCP ACK scan
-sW     nmap 192.168.1.1 -sW                        # TCP Window scan
-sM     nmap 192.168.1.1 -sM                        # TCP Maimon scan
```

## 4. Port Specification

```bash
-p 21                           # Scan specific port
-p 21-100                       # Scan port range
-p U:53,T:21-25,80              # TCP and UDP ports
-p-                             # All 65535 ports
-p http,https                   # Use service names
-F                              # Fast scan (100 ports)
--top-ports 2000                # Top 2000 ports
```

## 5. Timing and Performance

```bash
-T0 ... -T5                     # Scan speed (T0 slow, T5 fast)
--host-timeout <time>           # Max time per host
--min-rtt-timeout <time>        # Minimum probe timeout
--max-rtt-timeout <time>        # Maximum probe timeout
--min-hostgroup <size>          # Minimum parallel hosts
--max-hostgroup <size>          # Maximum parallel hosts
--min-parallelism <num>         # Minimum parallel probes
--max-parallelism <num>         # Maximum parallel probes
--scan-delay <time>             # Delay between probes
--max-scan-delay <time>         # Max delay between probes
--max-retries <tries>           # Retries per port
--min-rate <number>             # Minimum packets per second
--max-rate <number>             # Maximum packets per second
```

## 6. Service and Version Detection

```bash
-sV                              # Detect service versions
--version-intensity <0-9>        # Detection intensity
--version-light                  # Light scan
--version-all                    # Aggressive detection
-A                               # OS, version, scripts, traceroute
```

## 7. OS Detection

```bash
-O                               # Enable OS detection
--osscan-limit                   # Skip if unreliable
--osscan-guess                   # Guess aggressively
--max-os-tries <x>               
```

## 8. Firewall / IDS Evasion and Spoofing

```bash
-f                               # Fragment packets
--mtu <val>                      # Set MTU
-D                               # Decoy scan
-S                               # Spoof source IP
-g                               # Set source port
--proxies                        # Use proxies
--data-length <bytes>            # Append data
```

## 9. NSE Scripts

```bash
-sC                              # Default scripts
--script default                 # Equivalent
--script=banner                  # Run specific script
--script=http*                   # Wildcard
--script=http,banner              # Multiple scripts
--script "not intrusive"          # Exclude intrusive
--script-args                     # Script arguments
```

### 9.1 Example NSE Scripts

```bash
nmap -Pn --script=http-sitemap-generator scanme.nmap.org
nmap -n -Pn -p80 --open -sV -vvv --script banner,http-title -iR 1000
nmap -Pn --script=dns-brute domain.com
nmap -n -Pn -vv -O -sV --script smb-* 192.168.1.1
nmap --script whois* domain.com
nmap -p80 --script http-unsafe-output-escaping scanme.nmap.org
nmap -p80 --script http-sql-injection scanme.nmap.org
```

### 9.2 Web App Specific Scripts

```bash
nmap -p80 --script http-methods --script-args http-methods.test-all http://target
nmap -p80 --script http-headers http://target
nmap -p80 --script http-auth,http-auth-finder,http-auth-guess http://target
nmap -p80 --script http-enum http://target
nmap -p80 --script http-config-backup http://target
nmap -p80 --script http-userdir-enum http://target
nmap -p80 --script http-vhosts,http-iis-short-name-brute http://target
nmap -p80 --script http-dombased-xss,http-xssed,http-stored-xss,http-csrf 192.168.1.1
```

### 9.3 Advanced NSE Usage

```bash
nmap --script-args "userdb=users.txt,passdb=passlist.txt" -p21 ftp.target.com --script ftp-brute
nmap -p445 --script smb-enum-users,smb-enum-shares --script-args smbuser=admin,smbpass=password 192.168.1.100
nmap -p80 --script http-form-brute --script-args http-form-brute.hostname=target.com,http-form-brute.path=/login,http-form-brute.uservar=username,http-form-brute.passvar=password,http-form-brute.failmsg="invalid login" 192.168.1.1
```

## 10. Vulnerability Scanning Scripts

```bash
nmap --script vuln 192.168.1.1
nmap -sV --script vulners 192.168.1.1
nmap -p80 --script http-vuln-cve2015-1635 192.168.1.1
nmap -p80 --script http-vuln-cve2017-5638 192.168.1.1
nmap -p80 --script http-vuln-cve2017-1001000 192.168.1.1
```

## 11. Output Options

```bash
-oN <file>                       # Normal output
-oX <file>                       # XML output
-oG <file>                       # Grepable output
-oA <prefix>                     # All formats
--append-output                  # Append to existing file
-oG -                            # Output to screen
```

## 12. Output Analysis Tips

* Focus on open ports with exploitable services (HTTP, SMB, FTP).
* Closed ports respond; filtered ports are firewalled.
* Combine `-sV` and `-A` for banners and OS info.
* Use `--reason` to understand port states.
* Save all scans with `-oA` for reuse.
* Filter results using `grep open` or tools like `xsltproc` or `nmaptocsv`.
## 13. [StationX Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)
