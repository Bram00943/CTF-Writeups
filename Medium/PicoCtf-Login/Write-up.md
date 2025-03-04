# [Login] - CTF Write-up
## Web exploitation login page - PicoCTF practice

### 1. Overview
- Platform: CTF Platform PicoCTF
- Challenge name: Login (47k Solves)
- Difficulty: Medium
- Vulnerability: Hardcoded credentials
- Date: 04/01/25
- Impact: Allows unauthorized users to access the system by inspecting the code. Which can potentially lead to unauthorized access, data leaks. It is an easy exploitation and can weaken the trust of the application's security.

### 2. Reconnaissance & inital analysis
- First I used random login credentials while running burp proxy, to see if requests were being sent.
	* That was not the case.
- Then I inspected the page by 'inspect element' to see what files were used, and if there was anything interesting to see.
- I saw a javascript file which checked for a hard-coded username and password.

### 3. Exploitation steps

- I first inserted those hard-coded variables to see if it did anything.
	* Which it didn't.
- Then I went thinking why, where I came to the solution it's probably encoded in someway.
- I ran it through Burp's decoder tool and it turned out it was Base64 encoded.
- The username decoded gave back 'admin' and the password gave back the Flag. Now the last part was to enter those in the input fields.
	* Since the javascript code gives an alert with the Flag if the credentials are correct. So only decoding the Flag isn't good enough!

### 4. Proof of Concept (PoC)
YWRtaW4 → admin

cGljb0NURns1M3J2M3JfNTNydjNyXzUzcnYzcl81M3J2M3JfNTNydjNyfQ → picoCTF{53rv3r_53rv3r_53rv3r_53rv3r_53rv3r}

- Proof of completed CTF
![Proof completed CTF](/Medium/PicoCtf-Login/Proof.png)

### 5. Impact Analysis

- What could an attacker do with this vulnerability?
	* An attacker could easily retrieve the hardcoded credentials (username and password) from the JavaScript code, bypassing any authentication system.
	* With the correct username and password, they could gain unauthorized access to the system and potentially compromise its functionality.

- How severe is it? (High)
	* Hardcoded credentials can be a significant security risk, especially if they allow access to sensitive data or privileged actions. The exploitability is high because anyone inspecting the JavaScript can easily obtain the credentials and misuse them.

- Potential Business Impact
	* Confidential User Data: If the credentials give access to user accounts or sensitive user information, it could lead to a data breach.
	* Access to Admin/Privileged Functions: If the credentials provide administrative access, the attacker could modify, delete, or steal critical data, leading to system compromise.
	* Loss of Trust: If the vulnerability is discovered by external parties, it could damage the business’s reputation and trust among customers and users.

### 6. Mitigation & recommendations

- Mitigations
	1. Never hardcode sensitive information directly in the source code. This can be easily extracted by anyone who decides to inspect the code.
	2. Store sensitive data in Environment Variables or use a Secure Vault to manage them.
	3. Store credentials and sensitive data in secure Server-side storage. Do not expose them in client-side code or JavaScript.
	4. Ensure that role-based access control (RBAC) is in place where only authorized users can access certain data or functionalities. So even IF the credentials are compromised, an attacker's access should be limited.

- Recommendations
	1. Perform regular security audits and code reviews to make sure that no sensitive information is being exposed, such as hardcoded credentials in JavaScript or source code.
	2. Implement strong, modern authentication mechanisms, such as (OAuth or multi-factor authentication (MFA)), to further secure sensitive systems.
	3. Only if it's absolutely unavoidable to store sensitive data in the client-side code, obfuscate or encrypt the information before storing it. But NEVER rely solely on this as this can still be reverse-engineerd.
	4. Keep monitoring for unusual behavior that may indicate an attacker is trying to exploit hard-coded credentials, such as unauthorized logins or abnormal access patterns.
	5. Train developers to follow **secure coding practices** and educate them on the risks of hard-coding sensitive information.

### 7. Lessons learned & Takeaways.

- I learned that I need to recognize base64 encoded faster, and not try to decode the string to random encoding methods.