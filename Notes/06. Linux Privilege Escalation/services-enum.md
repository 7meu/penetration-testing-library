# 2. Linux Services Enumeration

## Network Information

```sh
ip a
cat /etc/hosts
```

## User and Login Information

```sh
lastlog
w
history
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```

## Scheduled Tasks

```sh
ls -la /etc/cron.daily/
ls -la /etc/cron.hourly/
ls -la /etc/cron.weekly/
ls -la /etc/cron.monthly/
cat /etc/crontab
ls -la /var/spool/cron/crontabs 2>/dev/null
```

## Process Information

```sh
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
ps aux | grep root
ps -ef
```

## Installed Packages and Binaries

```sh
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
sudo -V
ls -l /bin /usr/bin/ /usr/sbin/

# Check installed binaries against GTFOBins
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d'); do
    if grep -q "$i" installed_pkgs.list; then echo "Check GTFO for: $i"; fi
done
```

## System Calls

```sh
strace ping -c1 10.129.112.20
```

## Configuration Files and Scripts

```sh
find / -type f \( -name "*.conf" -o -name "*.config" \) -exec ls -l {} \; 2>/dev/null
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```
