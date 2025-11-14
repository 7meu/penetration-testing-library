# Misc Techniques

## 1. Flags for Better Results

### 1.1 Scan Types

* `-sA` - TCP ACK scan
* `-sS` - TCP SYN scan
* `-sT` - TCP connect scan

### 1.2 Host & Network Discovery

* `-PE` - Ping scan using ICMP ECHO request
* `--disable-arp-ping`

### 1.3 Packet Handling & Output

* `--packet-trace` - Show all packets sent and received
* `--reason` - Display the reason for specific results

### 1.4 Port Scanning

* `--top-ports=10` - Scan the top 10 most frequent ports
* `-p22` - Scan a specific port (e.g., port 22)
* `-F` - Scan the top 100 ports

### 1.5 Spoofing & Stealth Techniques

* `-D RND:5` - Generate 5 random decoy IP addresses
* `-S <IP>` - Set a specific source IP address
* `-e tun0` - Send requests through a specific network interface
* `--source-port 53` - Scan using a specific source port (e.g., 53)

---

## 2. Optimizing Nmap Scans

Reducing scan time helps evade IDS detection, while increasing speed may appear suspicious.

### 2.1 Timing & Performance Tweaks

* `--host-timeout 5s` - Set a timeout for each host
* `--scan-delay 5s` - Delay between scan attempts
* `--initial-rtt-timeout 50ms` - Set the initial RTT timeout
* `--max-rtt-timeout 100ms` - Set the max RTT timeout

---

## 3. Nmap Output Formats

* `-oN <filename>` - Save as normal text file
* `-oX <filename>` - Save as XML file
* `-oS <filename>` - Save as script (for Metasploit)
* `-oA <basename>` - Save in all formats (`.nmap`, `.xml`, `.gnmap`)
