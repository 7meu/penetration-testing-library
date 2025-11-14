# Linux Hardening

## Updates and Automation

### Debian/Ubuntu
```bash
sudo apt update
sudo apt upgrade
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Red Hat/CentOS

```bash
sudo yum update
sudo yum install yum-cron
sudo systemctl enable yum-cron
sudo systemctl start yum-cron
```

---

## Permission and File Auditing

### Find SUID binaries

```bash
find / -perm -4000 2>/dev/null
```

### Find world-writable files and directories

```bash
find / -perm -o+w -type f 2>/dev/null
find / -perm -o+w -type d 2>/dev/null
```

### Check custom library links

```bash
ldd <binary_name>
```

---

## Cron, Sudo, and User Environment

### Cron jobs

```bash
sudo crontab -l
crontab -l
```

### Sudo privileges

```bash
sudo -l
```

### Home directory check

```bash
ls -la ~
```

### Bash history

```bash
cat ~/.bash_history
```

---

## Accounts and Authentication

### Users and groups

```bash
cat /etc/passwd
cat /etc/group
```

### Login attempts

```bash
sudo cat /var/log/auth.log
sudo journalctl -u sshd.service
```

### Password policy

```bash
sudo cat /etc/login.defs
```

### Rotate password

```bash
sudo passwd <username>
```

### Old passwords file

```bash
sudo cat /etc/security/opasswd
```

---

## Packages, SELinux, and Cleanup

### Remove unnecessary packages

```bash
sudo apt remove <package_name>
sudo yum remove <package_name>
```

### SELinux status

```bash
sestatus
```

---

## System Auditing (Lynis)

### Clone Lynis

```bash
git clone https://github.com/CISOfy/lynis.git
cd lynis
```

### Run audit

```bash
sudo ./lynis audit system
```

---

## Mitigation

1. **Updates and Patching**
   * Automate updates.
   * Prioritize security patches.

2. **Configuration Management**
   * Audit SUID and writable paths.
   * Secure cron and sudo configurations.
   * Remove unused packages and services.
   * Use SELinux or AppArmor.

3. **User Management**
   * Limit privileged accounts.
   * Enforce strong passwords.
   * Monitor authentication logs.

4. **Audit**
   * Perform regular system and configuration audits.
   * Use established security baselines.
   * Run tools like Lynis.

5. **Automation**
   * Use configuration management tools.
   * Automate checks and remediation steps.
