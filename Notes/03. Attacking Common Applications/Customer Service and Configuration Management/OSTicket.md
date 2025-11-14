# osTicket

## 1. Credential Dumping with Dehashed

```bash
python3 dehashed.py -q inlanefreight.local -pid 5996447501
```

---

## 2. Subdomain Enumeration

```bash
cat ilfreight_subdomains
```

Tools: `sublist3r`, `amass`, `assetfinder`, etc.

---

## 3. osTicket Enumeration

### Identifying Instances

* Use **EyeWitness** to capture screenshots of potential portals.
* Look for cookies like `OSTSESSID` or footers referencing “powered by osTicket.”

### Creating a Ticket

1. Access the osTicket web interface.
2. Submit a support ticket.
3. Record the internal email address assigned (e.g., `940288@inlanefreight.local`).
4. Test this address for reuse on other systems such as Slack or GitLab.

---

## 4. Credential Testing

* Test recovered credentials manually on the osTicket portal and other related services.
* Review tickets for sensitive content such as:

  * Password reset emails.
  * Internal configuration data.
  * Employee usernames and contact information.

---

## 5. Exploiting Vulnerabilities

Search **Exploit-DB** for version-specific flaws:

* Remote File Inclusion
* SQL Injection
* File Upload Exploits
* Cross-Site Scripting
* SSRF

**Example: CVE-2020-24881 (SSRF in osTicket v1.14.1)**
Allows internal port scanning and limited access to internal endpoints.

---

## 6. Social Engineering

* Engage support staff under pretense of technical issues to gather system insights.
* Subtle questions may reveal workflow details or contact patterns useful for further enumeration.

---

## 7. Mitigation

* Limit external exposure of helpdesk systems.
* Enforce MFA across all external services.
* Educate staff on phishing and social engineering risks.
* Require strong, unique passwords and regular rotation.
