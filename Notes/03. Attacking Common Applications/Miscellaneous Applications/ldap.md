# Attacking LDAP

## 1. LDAP Fundamentals

**Definition:**
Lightweight Directory Access Protocol (LDAP) is a protocol used to access and manage directory information, typically for authentication and authorization.

**Purpose:**

* Centralizes identity management (users, groups, devices).
* Supports Single Sign-On (SSO) and access control.
* Stores hierarchical organizational data.

**Common Ports:**

* `389` - LDAP (cleartext)
* `636` - LDAPS (LDAP over SSL/TLS)

**Data Model:**

* Hierarchical structure called the **Directory Information Tree (DIT)**.
* Each entry identified by a **Distinguished Name (DN)**.
* Attributes (e.g., `cn`, `uid`, `mail`) define properties.
* **Object classes** determine schema constraints.

---

## 2. LDAP Operations

**Requests:**

* `bind` - Authenticate a user.
* `unbind` - Terminate a session.
* `search` - Query for entries.
* `add`, `modify`, `delete` - Manage entries.
* `compare` - Check attribute values.

**Responses:**
Include result codes, matched DNs, or returned entries.

---

## 3. Implementations

* **OpenLDAP:** Open-source and flexible.
* **Microsoft Active Directory:** Integrates LDAP with Kerberos and domain services.

---

## 4. LDAP Injection

### Concept

LDAP Injection occurs when user input is inserted unsafely into LDAP queries, altering their logic — similar to SQL injection.

### Example

```bash
(&(uid=$username)(userPassword=$password))
```

Attacker inputs `*)(uid=*` to bypass authentication.

### Common Operators

| Symbol     | Function   |    |
| :--------- | :--------- | -- |
| `*`        | Wildcard   |    |
| `()`       | Grouping   |    |
| `          | `          | OR |
| `&`        | AND        |    |
| `!`        | NOT        |    |
| `>=`, `<=` | Comparison |    |
| `\`        | Escape     |    |

### Example Payloads

* Extract all users:

  ```
  *)(|(cn=*)(mail=*))
  ```
* Privilege escalation:

  ```
  admin*)(|(objectClass=*))
  ```

**Impact:**

* Unauthorized access.
* Data leakage.
* Privilege escalation or account takeover.

---

## 5. LDAP Enumeration

### Nmap

```bash
nmap -p 389,636 -sV -sC <target_ip>
```

### ldapsearch

**Anonymous bind:**

```bash
ldapsearch -x -h <target_ip> -p 389 -b "dc=example,dc=com" "(objectClass=*)"
```

**Authenticated bind:**

```bash
ldapsearch -x -h <target_ip> -p 389 -D "cn=admin,dc=example,dc=com" -w "password" -b "dc=example,dc=com" "(objectClass=*)"
```

### Common Enumeration Goals

* **Users:** Look for attributes like `uid` or `sAMAccountName`.
* **Groups:** Identify roles or privileges.
* **Base DNs:** Try defaults such as `dc=example,dc=com`.

### Tools

* **LDAPenum** - Perl-based LDAP enumeration.
* **Medusa** - Brute-force LDAP authentication.
* **enum4linux** - Windows LDAP/SMB enumeration.

  ```bash
  enum4linux -a <target_ip>
  ```
* **GUI Clients:** JXplorer, Apache Directory Studio, LDAPAdmin.

---

## 6. LDAP Injection Exploitation

### Steps

1. Insert LDAP control characters in input fields.
2. Observe responses (authentication bypass, error messages).
3. Attempt data extraction via altered queries.

### Example Bypass

Input:

```
*)(uid=*)
```

Bypasses standard authentication filters.

### Manual Data Extraction

Using `ldapsearch` after injection to query sensitive attributes directly.

---

## 7. Mitigation

**1. Input Sanitization**

* Reject special LDAP characters.
* Escape user inputs properly.

**2. Parameterized Queries**

* Separate code logic from user data.

**3. Principle of Least Privilege**

* Restrict LDAP bind account permissions.

**4. Access Control**

* Limit exposure of sensitive attributes.

**5. Encryption**

* Use **LDAPS** to prevent interception.

**6. Monitoring & Auditing**

* Review logs for unusual LDAP activity.
* Regular vulnerability scans and pentests.

**7. WAF Rules**

* Deploy a web application firewall to detect LDAP injection attempts.

---

## 8. Commands Summary

```bash
# Port Scan
nmap -p 389,636 -sV -sC <target_ip>

# LDAP Anonymous Bind
ldapsearch -x -h <target_ip> -p 389 -b "dc=example,dc=com" "(objectClass=*)"

# LDAP Authenticated Bind
ldapsearch -x -h <target_ip> -p 389 -D "cn=admin,dc=example,dc=com" -w "password" -b "dc=example,dc=com" "(objectClass=*)"

# Enum4linux (Windows)
enum4linux -a <target_ip>
```
