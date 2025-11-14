# Initial Enumeration

## Network Discovery and Traffic Analysis

#### Capture traffic using Wireshark
```bash
sudo wireshark
```

#### Capture packets on interface ens224 using tcpdump

```bash
sudo tcpdump -i ens224
```

#### Run Responder for LLMNR, NBT-NS, and MDNS poisoning

```bash
sudo responder -I ens224 -A
```

#### Ping sweep using fping

```bash
fping -asgq 172.16.5.0/23
```

### Responder

```bash
sudo responder -I <interface> -A
```

## Host and Service Enumeration

#### Perform aggressive scan on hosts listed in hosts.txt

```bash
sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum
```

#### Aggressive scan on a specific host

```bash
sudo nmap -A 172.16.5.100
```

## User Enumeration

#### Clone kerbrute repository

```bash
sudo git clone https://github.com/ropnop/kerbrute.git
```

#### View make options

```bash
make help
```

#### Build kerbrute

```bash
sudo make all
```

#### Run kerbrute binary

```bash
./kerbrute_linux_amd64
```

#### Check current PATH

```bash
echo $PATH
```

#### Move kerbrute to a directory in PATH

```bash
sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute
```

#### Enumerate valid Active Directory users

```bash
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```
