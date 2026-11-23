ArcadeVault Web Application Security Assessment

A penetration test of a deliberately vulnerable e-commerce web application, performed against a local training lab and documented against the OWASP Top 10:2025.

Overview

This project covers a black-box security assessment of the ArcadeVault web shop, a video-game store built for security training. The test covered the storefront, REST API, admin back-office, and mod registry. Twelve weaknesses were identified and rated with CVSS 3.1.

Scope and Method
Target: ArcadeVault running locally in Docker on port 9098
Approach: Black box, with access to the running application only
Methodology: BSI Durchführungskonzept für Penetrationstests and the OWASP Web Security Testing Guide
Tools: Burp Suite (Proxy, Repeater, Intruder), curl, John the Ripper, Kali Linux
Rating: CVSS 3.1 base scores, weaknesses mapped to CWE
Findings Summary
ID	Finding	OWASP Category	Severity
AV-01	IDOR on order lookup	A01 Broken Access Control	Medium
AV-02	Negative cart quantity accepted	A06 Insecure Design	High
AV-03	Reflected XSS in search	A05 Injection	Medium
AV-04	Stored XSS in review	A05 Injection	Medium
AV-05	Guest access to audit log, plaintext password	A01, A09	High
AV-06	Exposed backup directory	A02 Security Misconfiguration	High
AV-07	Unsigned theme update accepted	A08 Software and Data Integrity	Medium
AV-08	Forced browsing to staff refund	A01 Broken Access Control	High
AV-09	Debug endpoint exposes secrets	A02 Security Misconfiguration	High
AV-10	Stack trace and secrets in error response	A10 Mishandling of Exceptional Conditions	High
AV-11	Dependency confusion in mod install	A03 Software Supply Chain	High
AV-12	MFA not enforced on admin session	A07 Authentication Failures	High
Key Takeaways
Authorisation must be enforced on the server for every endpoint, not only in the user interface.
Secrets such as signing keys and database credentials should never appear in application responses or logs.
Passwords should be stored with a slow, salted hash, and MFA must be enforced server-side.
Input and output handling must be consistent, including output encoding and XML parser hardening.
Skills Demonstrated

Web application penetration testing, OWASP Top 10 analysis, Burp Suite, API testing, CVSS scoring, technical report writing.

Note

This assessment was performed against a deliberately vulnerable local training lab as part of a university course. No real systems, customers, or production data were involved. The full report, which includes detailed evidence, is available on request.
