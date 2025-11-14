# Services

## Enumeration Commands

```powershell
wmic product get name
netstat -ano | findstr 6064
get-process -Id 3324
get-service | ? {$_.DisplayName -like 'Druva*'}
```

## Exploitation (Setup) Commands

```powershell
python3 -m http.server 8080
nc -lvnp 9443
Set-ExecutionPolicy Bypass -Scope Process
```

## Verification Commands

```powershell
whoami
hostname
```

## Key Concepts

* Third-party software can introduce vulnerabilities, especially when running as SYSTEM.
* Druva inSync (e.g., 6.6.3) is vulnerable to command injection via RPC and can provide SYSTEM-level execution.

## Approach, Commands, Tools, and Techniques

1. **Enumeration**

   * Identify installed software:

     ```powershell
     wmic product get name
     ```
   * Check if the vulnerable service is listening:

     ```powershell
     netstat -ano | findstr 6064
     ```
   * Map the PID to a process:

     ```powershell
     get-process -Id <PID>
     ```
   * Verify the Druva service:

     ```powershell
     get-service | ? {$_.DisplayName -like 'Druva*'}
     ```

2. **Exploitation**

   * Modify the PoC to run a reverse shell.
   * Host the payload with:

     ```powershell
     python3 -m http.server 8080
     ```
   * Start listener:

     ```bash
     nc -lvnp 9443
     ```
   * Bypass execution policy:

     ```powershell
     Set-ExecutionPolicy Bypass -Scope Process
     ```
   * Execute the modified PoC to achieve SYSTEM command execution.

3. **Verification**

   * Confirm SYSTEM privileges:

     ```powershell
     whoami
     ```
   * Confirm the target machine:

     ```powershell
     hostname
     ```
