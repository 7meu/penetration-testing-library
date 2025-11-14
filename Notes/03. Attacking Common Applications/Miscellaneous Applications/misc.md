# Other Notable Applications

## 1. Methodology over Specific Tools

The priority is developing a **repeatable methodology** rather than relying solely on tool-specific knowledge. Techniques learned here can be applied across a variety of applications, regardless of platform or technology.

---

## 2. Comprehensive Enumeration

* Conduct full **network and service enumeration** to discover all reachable applications.
* Tools like **EyeWitness** are helpful for visual cataloging of discovered web services and identifying duplicates or hidden instances.
* Enumerate every port and service before focusing on specific vulnerabilities.

---

## 3. Adaptability and Persistence

* Expect to face **unfamiliar or proprietary applications**.
* Persistence and logical reasoning are more valuable than memorizing tools.
* Filtering out scan noise and analyzing subtle behaviors often reveals overlooked vulnerabilities.

---

## 4. Abuse of Built-In Functionality

* Many web and enterprise applications include **legitimate administrative functions** that can be abused.
* Default credentials and configuration weaknesses remain extremely common.
* Always review exposed features, especially those related to file handling, API access, and authentication.

---

## 5. Understanding Application Logic

* Deeply understanding the application’s **intended workflows and data flow** enables the discovery of logic-based flaws.
* Review how inputs are validated, how sessions are managed, and how data is processed between components.

---

## 6. Notable Applications and Vulnerabilities

### Axis2

* May allow **RCE** via AAR file upload.
* Frequently left with weak or **default credentials**.

### WebSphere

* Known for **RCE through WAR deployment** and **default logins** (`system:manager`).
* Legacy installations are often unpatched.

### Elasticsearch

* Older versions contain **unauthenticated RCE** and **information disclosure** flaws.
* Forgotten instances frequently remain exposed on networks.

### Zabbix

* Vulnerable to **SQL injection**, **auth bypass**, **LDAP credential leaks**, and **remote code execution**.
* Its **API** can also be exploited to execute arbitrary commands.

### Nagios

* Common issues include **RCE**, **privilege escalation**, **SQLi**, **code injection**, and **XSS**.
* Default credentials such as `nagiosadmin:PASSW0RD` are still widely observed.

### WebLogic

* Numerous **CVE-listed RCE vulnerabilities**, often unauthenticated.
* Ensure all versions are patched, especially those using older Java components.

### Wikis / Intranet Platforms (MediaWiki, SharePoint)

* Exposed **search functionality** and **document uploads** can leak credentials or sensitive internal data.
* Historically affected by **RCE and XSS** issues.

### DotNetNuke (DNN)

* Prone to **authentication bypass**, **directory traversal**, **file upload bypass**, and **arbitrary file download**.

### vCenter

* Central to VMware infrastructure; compromise often leads to **domain-wide access**.
* Issues include **default credentials**, **Struts 2 RCE**, and **OVA upload vulnerabilities**.
* Windows-based vCenter servers can allow **privilege escalation**.
