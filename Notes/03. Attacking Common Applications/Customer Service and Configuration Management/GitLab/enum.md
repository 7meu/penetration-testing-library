# GitLab Discovery and Enumeration

## 1. Discovery

Access the target GitLab instance directly in a browser to confirm its presence through the visible login interface.

---

## 2. Enumeration

### Version Enumeration

* Version information is usually available at `/help` (may require authentication).
* Avoid version probing or low-risk exploitation attempts without explicit authorization.

### Public Projects (`/explore`)

* Visit `/explore` to locate publicly accessible repositories.
* Review discovered projects for:

  * Configuration or deployment files.
  * API scripts or automation tokens.
  * Exposed SSH keys or credentials.
  * Source code vulnerabilities.

### Registration (`/users/sign_up`)

* Check if registration is enabled.
* Attempt to register an account to determine:

  * Username enumeration (existing usernames trigger “Username has already been taken”).
  * Email enumeration (existing emails trigger “Email has already been taken”).
* Successful registration may reveal more internal repositories.

### Credential Testing

* Use credentials obtained from OSINT sources (e.g., Dehashed) to test logins.
* Never brute-force without authorization.

### Internal Projects

* After login, revisit `/explore` for additional private or internal repositories.
* Inspect accessible code and configurations for secrets or credentials.

### Search Functionality

* Use GitLab’s internal search bar to locate sensitive terms such as `password`, `api_key`, `token`, or `private_key`.

### Groups and Snippets

* Review group workspaces and user snippets for exposed data or credentials.
* Snippets often include scripts or debug outputs containing useful details.

---

## 3. Security Considerations

* **Two-Factor Authentication (2FA):** Disabled by default — enable it to reduce account takeover risk.
* **Fail2Ban:** Helps prevent brute-force attempts against login endpoints.
* **IP Whitelisting:** Restrict public GitLab access to trusted IP ranges.
* **Account Registration Controls:** Enforce company-domain registration and require admin approval.
