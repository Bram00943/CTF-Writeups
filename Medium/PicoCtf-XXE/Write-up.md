# [SOAP] - CTF Write-up
## XXE Data retrieval - PicoCTF Practice

### 1. Overview
- Platform: PicoCTF
- Challenge name: SOAP (13K solves)
- Difficulty: Medium
- Vulnerability: XXE
- Date: 11/01/25
- Impact: In this case an attacker could read sensitive files on the server.

### 2. Reconnaissance & inital analysis

- First I searched up how an XML payload is formatted & looked up what the vulnerabilities of an XXE attack are.
- After that I learned about 'External Entity', and what that does.
- Then I used the site as intended and looked in Burp what the body of the requests and responses contained.
- I did not find anything particular interesting, nor anything interesting in the source files.

### 3. Exploitation steps

1. First I tried some XML payloads which resulted in an error to see what kind of behaviour the server had.
2. Then I did some googling on how to read an file using an XXE attack, and landed on PortSwigger which had a topic about XXE Injections.
3. There I saw something that looked like what I needed for my CTF challenge.
4. I first read what it would do and then imagined the result of what happened if I sended that request.
5. When I forwarded the modified request with my XXE injection, I got a bunch of text back including an error, but also the Flag. The text was interesting for further exploitation if this was a real world situation.

### 4. Proof of Concept (PoC)

* Modified XML request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE data [
	<!ENTITY xxe SYSTEM "file:///etc/passwd">
	]>

	<data>
		<ID>&xxe;</ID>
	</data>
```
* tests
![Test1](/Medium/PicoCtf-XXE/images/test(1).png)
![Test2](/Medium/PicoCtf-XXE/images/tests(2).png)
![Test3](/Medium/PicoCtf-XXE/images/tests(3).png)

- Proof of completed CTF
![Proof completed CTF](/Medium/PicoCtf-XXE/images/Proof.png)

### 5. Impact Analysis
- What could an attacker do with this vulnerability?
	* An attacker could exploit XXE to read sensitive files on the server like in this CTF /etc/passwd or application configuration files (which may contain secrets or credentials).
	* By using recusive entities in XML, an atacker could cause excessive resource consumption (e.g. CPU and Memory), leading to a DoS, and potentially crashing the service.
	* If the server allows, an attacker could use XXe to make HTTP requests to internal services or even trigger other vulnerabilities that lead to remote code execution.
	* Not only files like /etc/passwd can be read, but XXE can also expose other confidential data, such as internal database dumps, configuration files, or API endpoints.
- How severe is it? (Moderate - High)
	* Moderate if an attacker can only read non-sensitive or cause minimal DoS effects.
	* High if an attacker can access sensitive configuration files, user data, or escalate privileges (e.g., by reading /etc/passwd, accessing internal services, or exploiting it to trigger further vulnerabilities like RCE).
- Potential Business Impact
	* If an attacker can read sensitive files this can result in the exposure of customer data, application secrets, or credentials, which could lead to a __data breach__ and potentially legal/regulatory consequences (e.g. GDPR, HIPAA violations).
	* A successful exploitation of XXE could damage the company's reputation if customers or partners learn that their data or the company's systems were compromised.
	* If an attacker exploits XXE to trigger a DoS attack, the availability of the service can be affected, potentially causing downtime and loss of business.

### 6. Mitigation & recommendations

- Mitigations
	1. The most effective way to prevent XXE is to disable DTD's (Document Type Definitions) and external entity processing in the XML parser.
	2. Implement secure XML parsers that do not allow external entities. Use libraries that do not process
	```<!DOCTYPE> ```declarations or external resources by default.
		* Like JSON as alternative to XML can entirely mitigate XXE vulnerabilities since JSON does not support external entities.

- Recommendations
	1. Use strong input validation to restrict XML input to known and safe formats. Prevent <!DOCTYPE> declarations or other XML features that can be leveraged for XXE.
	2. Conduct regular security audits and pen-testing to identify vulnerabilities like XXE. Automated scanning tools can help identify risky XML parsing configurations, and manual testing ensures edge cases are covered.
	3.Educate developers about the risks of XXE and secure coding practices. Provide training on safe XML parsing, understanding vulnerabilities, and how to mitigate them in code to prevent future attacks.


### 7. Lessons learned & Takeaways.

- I learned what an External Entity is in XML
- By completing this CTF; I learned you can, if not secured, read files even if the request/feature has nothing todo with files.
