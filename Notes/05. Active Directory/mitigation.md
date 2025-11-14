# Mitigation

## Hardening Active Directory

### Step One: Document and Audit
Perform recurring audits of:
- OU, computer, user, and group naming conventions  
- DNS, DHCP, and network configurations  
- GPOs and their application  
- FSMO roles  
- Application inventory  
- Host locations  
- Trust relationships  
- Privileged accounts  

### People
- Strong password policy and filters  
- Routine service account password rotation  
- Remove local admin rights from workstations  
- Disable the default RID-500 local admin  
- Implement tiered administration  
- Restrict privileged group membership  
- Use Protected Users  
- Disable delegation for admin accounts  

### Protected Users Group
Restrictions applied:
- No constrained/unconstrained delegation  
- No plaintext credential caching  
- No NTLM, DES, or RC4  
- No caching long-term keys after TGT acquisition  
- TGT renewal limited to original 4-hour TTL  

### Processes
- AD asset management  
- Access control, MFA  
- Hardened host provisioning  
- Regular cleanup of stale objects  
- Removal of legacy systems  
- Scheduled audits of users, groups, hosts  

### Technology
- Regular AD reviews  
- Tools: BloodHound, PingCastle, Grouper  
- Remove credentials in descriptions  
- Check SYSVOL for sensitive data  
- Use gMSA/MSA  
- Disable unconstrained delegation  
- Harden jump hosts  
- Set `ms-DS-MachineAccountQuota` to 0  
- Disable Print Spooler where possible  
- Disable NTLM on domain controllers  
- Require SSL/EPA for CA services  
- Enable SMB and LDAP signing  
- Reduce AD enumeration exposure  
- Regular penetration tests  
- Validate backups and DR plans  
- Restrict anonymous access and null sessions  

---

## Protections by MITRE ATT&CK Section

### External Reconnaissance (T1589)
- Limit public information  
- Scrub metadata  

### Internal Reconnaissance (T1595)
- Monitor network traffic  
- Use firewalls and NIDS  
- SIEM monitoring  
- Tune Windows Firewall and EDR  

### Poisoning (T1557)
- Enable SMB signing  
- Enforce encrypted traffic  

### Password Spraying (T1110/003)
- Monitor for logon failures  
- Apply strong password policies  
- Use account lockout policies  
- Implement MFA  

### Credentialed Enumeration (TA0006)
- Monitor for abnormal CLI/RDP activity  
- Network segmentation  

### Living Off the Land (LOTL)
- Behavioral baselines  
- Command shell monitoring  
- AppLocker enforcement  

### Kerberoasting (T1558/003)
- Disable RC4  
- Enforce strong passwords  
- Use gMSAs  
- Audit SPNs and permissions  

---

## MITRE ATT&CK Breakdown
- Outlines use of tactics, techniques, and sub-techniques  
- Example: Kerberoasting falls under Credential Access (TA0006) - T1558.003  

---

## Key Takeaways
- Hardening requires layered defenses across people, processes, and technology  
- Regular audits are essential  
- MITRE ATT&CK mapping helps identify weak points  
- Baseline security hygiene is more effective than additional tools  

---

# 30. Additional AD Auditing Techniques

## Creating an AD Snapshot with AD Explorer
- Advanced viewer/editor for AD  
- Supports snapshots for offline comparison  
- Useful for detecting changes over time  

### Usage
- Launch AD Explorer  
- Browse objects  
- Create snapshot (File - Create Snapshot)  

---

## PingCastle
- AD security assessment tool  
- Produces maps, graphs, and detailed reports  
- Highlights misconfigurations and vulnerabilities  

### Usage
- Run `PingCastle.exe`  
- Use interactive mode or command flags  
- Generate health check reports  
- If issues occur, set system date before July 31, 2023  

---

## Group3r
- Audits Group Policy for security issues  
- Requires domain context  

### Usage
```bash
group3r.exe -f output.log
group3r.exe -s
```

---

## ADRecon

* Collects comprehensive AD information
* Not stealthy but very thorough

### Usage

* Run `ADRecon.ps1`
* Review HTML/CSV reports
* Use `-GenExcel` if Excel is installed

### Output Includes

* Domains, forests, trusts
* Sites, subnets
* Password policies
* DCs, users, groups
* OUs, GPOs, DNS
* Printers, computers
* LAPS and BitLocker data

---

## Reporting

* Critical for communicating findings
* Helps justify remediation and funding
* Multiple tools provide the most complete picture
