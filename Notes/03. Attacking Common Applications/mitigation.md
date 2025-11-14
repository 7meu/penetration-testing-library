# Application Hardening

## 1. Application Inventory

* Maintain a complete inventory of all applications, including deprecated and shadow IT systems.
* Use tools like **Nmap** and **EyeWitness** to identify exposed services.

## 2. General Hardening Tips

### 2.1 Secure Authentication

* Enforce strong passwords.
* Change default administrative credentials.
* Disable default admin accounts.
* Use multi-factor authentication.

### 2.2 Access Controls

* Restrict access to sensitive interfaces.
* Apply proper file and folder permissions.

### 2.3 Disable Unsafe Features

* Disable features that allow code execution, such as WordPress theme/plugin file editing.

### 2.4 Regular Updates

* Apply security patches consistently.
* Keep all applications updated.

### 2.5 Backups

* Maintain regular backups of application data and databases.
* Store backups securely in a secondary location.

### 2.6 Security Monitoring

* Use monitoring tools and Web Application Firewalls.
* Enable alerting for suspicious activity.

### 2.7 LDAP Integration

* Integrate with Active Directory for SSO where possible.
* Enforce organizational password and authentication policies.

### 2.8 Least Privilege

* Apply least-privilege principles for all application roles and permissions.

### 2.9 Limit External Exposure

* Reduce the number of externally exposed applications and admin interfaces.

## 3. Application-Specific Hardening Tips

### 3.1 WordPress

* Use security plugins such as WordFence.

### 3.2 Joomla

* Use plugins like AdminExile to protect admin login.

### 3.3 Drupal

* Hide or relocate the admin login page.

### 3.4 Tomcat

* Restrict access to Manager and Host-Manager to localhost or trusted IPs.

### 3.5 Jenkins

* Use the Matrix Authorization Strategy plugin for role-based access control.

### 3.6 Splunk

* Change the default credentials.
* Ensure proper authentication is enforced.

### 3.7 PRTG

* Change default credentials and keep software updated.

### 3.8 osTicket

* Restrict internet accessibility where possible.

### 3.9 GitLab

* Enforce signup restrictions and domain whitelisting.

## 4. Continuous Improvement

* Maintain an updated application inventory.
* Schedule regular penetration tests and security assessments.
* Address and document remediation steps.
* Promote security awareness across the organization.

## 5. Secure Configuration Management

* Treat configuration files as sensitive assets.
* Avoid storing cleartext secrets in configs.
* Use environment variables instead.
* Apply access controls to config files.
* Use Infrastructure as Code for standardized, auditable configuration.

## 6. Input Validation and Output Encoding

* Validate all user input using whitelisting where possible.
* Sanitize input to prevent injection attacks.
* Use proper output encoding to prevent XSS.

## 7. Session Management

* Use strong, unpredictable session IDs.
* Configure appropriate session expiration.
* Enforce HSTS.
* Set `Secure` and `HttpOnly` cookie flags.

## 8. Error Handling and Logging

* Avoid leaking internal details in error messages.
* Implement custom error pages.
* Log security events and store logs securely.
* Monitor logs for anomalies.

## 9. Dependency Management

* Use SCA tools to detect vulnerable dependencies.
* Keep third-party libraries updated.
* Ensure supply chain integrity by using trusted sources only.

## 10. Database Security

* Apply least privilege to database accounts.
* Validate all input before database queries.
* Use stored procedures where appropriate.
* Encrypt sensitive data at rest and in transit.

## 11. Network Security

* Apply strict firewall rules.
* Deploy IDS/IPS where needed.
* Use network segmentation to isolate critical services.

## 12. Security Awareness Training

* Train developers on secure coding practices.
* Educate users about operational security.

## 13. Incident Response Planning

* Maintain an incident response plan.
* Test and update the plan regularly.
