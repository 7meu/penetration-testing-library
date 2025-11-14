# Active Directory: Full Attack Name

## 1. Initial Access

These attacks focus on gaining the first foothold in the network.

1. Phishing Attack  
2. Malicious Attachments / Macros  
3. Credential Stuffing  
4. Pass-the-Cookie Attack  
5. Pass-the-Ticket Attack  
6. Malicious USB / Hardware Injection  
7. Exposed SMB Shares  
8. VPN Credential Hijacking  
9. Zero-Day Exploitation  
10. LLMNR / NBT-NS Poisoning  
11. Evil Twin Attack  
12. Watering Hole Attack  
13. Supply Chain Attack  

---

## 2. Credential Dumping Attacks

These attacks extract credentials from memory, disk, or authentication processes.

1. Mimikatz  
2. DCSync Attack  
3. LSASS Dumping  
4. SAM & SYSTEM Hive Dumping  
5. Kerberos Ticket Extraction  
6. NTDS.dit Dumping  
7. WDigest Credential Theft  
8. LSASS Memory Injection  
9. Cached Credentials Dumping  
10. Browser Credential Theft  
11. DPAPI Abuse  

---

## 3. Privilege Escalation Attacks

Used to obtain elevated access rights.

1. Kerberoasting  
2. AS-REP Roasting  
3. Token Impersonation  
4. Pass-the-Hash  
5. Print Spooler Exploits  
6. Unconstrained Delegation Abuse  
7. Constrained Delegation Abuse  
8. Local Privilege Escalation  
9. SeBackupPrivilege Abuse  
10. SeRestorePrivilege Abuse  
11. Kerberos S4U2Self Abuse  
12. Kerberos S4U2Proxy Abuse  
13. DNSAdmins Group Abuse  
14. Active Directory Certificate Services Abuse  

---

## 4. Lateral Movement

Techniques for moving between systems once initial access is gained.

1. Pass-the-Hash  
2. Pass-the-Ticket  
3. SMB Relay Attack  
4. PSExec  
5. RDP Hijacking  
6. WMI Lateral Movement  
7. BloodHound Path Discovery  
8. DCOM Execution  
9. WinRM Lateral Movement  
10. Printer Bug  
11. PetitPotam  
12. Domain Trust Abuse  
13. Forest Trust Abuse  

---

## 5. Persistence and Domain Takeover

Used to maintain long-term access or full control of the domain.

1. Golden Ticket  
2. Silver Ticket  
3. Skeleton Key  
4. AdminSDHolder Modification  
5. GPO Hijacking  
6. SID History Injection  
7. Shadow Credentials Attack  
8. RBCD Exploitation  
9. DCShadow Attack  
10. WMI Event Subscription  
11. Registry Run Keys  
12. AD Recycle Bin Abuse  
13. ADFS Token Forgery  
14. Azure AD Connect Exploitation  

---

## 6. NTLM and Kerberos Exploitation

Attacks targeting authentication protocols.

1. NTLM Relay  
2. NTLMv1 Downgrade  
3. Kerberos Downgrade  
4. Kerberos Overpass-the-Hash  
5. Kerberos Pre-Auth Brute Force  
6. PAC Tampering  
7. TGT Delegation Abuse  
8. Kerberos Bronze Bit Attack  
9. LDAP Relay  

---

## 7. Data Exfiltration & Long-Term Persistence

Attacks focused on data theft or maintaining covert access.

1. LDAP Domain Dump  
2. Group Policy Preferences Credential Extraction  
3. LSA Secrets Dumping  
4. SYSVOL Credential Theft  
5. MSSQL Server Exploitation  
6. Shadow Copy Abuse  
7. Cloud Sync Abuse  
8. ADFS Token Signing Certificate Theft  
9. Azure AD Exploitation  

---

## 8. Miscellaneous AD Attacks

Additional techniques that support reconnaissance or privilege abuse.

1. AD Reconnaissance with PowerView
2. AD Object Permission Abuse
3. Unconstrained Delegation Abuse
