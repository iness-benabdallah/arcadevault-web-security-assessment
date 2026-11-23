# ArcadeVault Web Application Security Assessment

A black-box penetration test of a deliberately vulnerable e-commerce application, documented against the **OWASP Top 10:2025**.

---

## 📌 Overview

This project covers a black-box security assessment of the ArcadeVault web shop, a video-game store built for security training. The assessment covered the storefront, REST API, admin back-office, and mod registry.

A total of **12 security weaknesses** were identified, documented, and rated using CVSS 3.1.

## 🎯 Scope & Methodology

- **Target:** ArcadeVault running locally in Docker on port `9098`
- **Approach:** Black-box testing, with access to the running application only
- **Methodology:** BSI Durchführungskonzept für Penetrationstests and the OWASP Web Security Testing Guide
- **Tools:** Burp Suite (Proxy, Repeater, Intruder), cURL, John the Ripper, Kali Linux
- **Risk Assessment:** CVSS 3.1 base scores
- **Classification:** Common Weakness Enumeration (CWE)

---

## 📊 Findings Summary

| ID | Finding | OWASP Category | Severity |
|---|---|---|---|
| AV-01 | IDOR on order lookup | A01 Broken Access Control | Medium |
| AV-02 | Negative cart quantity accepted | A06 Insecure Design | High |
| AV-03 | Reflected XSS in search | A05 Injection | Medium |
| AV-04 | Stored XSS in review | A05 Injection | Medium |
| AV-05 | Guest access to audit log, plaintext password | A01, A09 | High |
| AV-06 | Exposed backup directory | A02 Security Misconfiguration | High |
| AV-07 | Unsigned theme update accepted | A08 Software and Data Integrity | Medium |
| AV-08 | Forced browsing to staff refund | A01 Broken Access Control | High |
| AV-09 | Debug endpoint exposes secrets | A02 Security Misconfiguration | High |
| AV-10 | Stack trace and secrets in error response | A10 Mishandling of Exceptional Conditions | High |
| AV-11 | Dependency confusion in mod install | A03 Software Supply Chain | High |
| AV-12 | MFA not enforced on admin session | A07 Authentication Failures | High |

---

## 🔎 Findings in Detail

### AV-01: IDOR on Order Lookup
**CWE-639 · CVSS 5.3 (Medium) · A01**

Insecure direct object reference on `GET /api/orders/{id}`. Changing the order number in Repeater returned another customer's order with `200 OK`. The server authorises by identifier alone, not by session ownership.

### AV-02: Negative Cart Quantity Accepted
**CWE-840, CWE-20 · CVSS 7.5 (High) · A06**

Business logic flaw in `PUT /api/cart/items/{sku}`. A client-supplied quantity of `-50` was stored and turned checkout into a refund. The server trusts the request body without server-side range validation.

### AV-03: Reflected XSS in Search
**CWE-79 · CVSS 6.1 (Medium) · A05**

Unencoded search input is reflected into the results page via `GET /?q=`, executing `<script>` in the browser. Output encoding and a Content Security Policy are missing.

### AV-04: Stored XSS in Review
**CWE-79 · CVSS 5.4 (Medium) · A05**

The review payload is persisted via `POST /api/reviews` and rendered as HTML on reload, so the script runs for every visitor. This persistent injection has a wider reach than AV-03.

### AV-05: Guest Access to Audit Log with Plaintext Password
**CWE-285, CWE-532 · CVSS 7.5 (High) · A01, A09**

`GET /api/admin/logs` returned audit lines to an unauthenticated guest session (`loggedIn: false`). Login events stored plaintext passwords, enabling account takeover through leaked credentials.

### AV-06: Exposed Backup Directory
**CWE-538 · CVSS 7.5 (High) · A02**

Directory listing on `/backup` exposed a SQL dump with user records and unsalted MD5 password hashes, reachable without a session. This is a security misconfiguration with direct credential impact.

### AV-07: Unsigned Theme Update Accepted
**CWE-345, CWE-353 · CVSS 6.5 (Medium) · A08**

`POST /api/admin/theme/update` trusted a client-computed checksum, so the integrity check offers no protection. Anyone who can send the request can publish JavaScript served to all visitors.

### AV-08: Forced Browsing to Staff Refund
**CWE-425 · CVSS 7.5 (High) · A01**

Staff endpoints were reachable by direct URL. `POST /api/staff/orders/{id}/refund` processed a refund for a guest session, showing missing server-side role enforcement.

### AV-09: Debug Endpoint Exposes Secrets
**CWE-215, CWE-200 · CVSS 7.5 (High) · A02**

`GET /debug` returned `"debug": true` and `"env": "production"`, along with JWT and session signing secrets and a database connection string, to an unauthenticated visitor.

### AV-10: Stack Trace and Secrets in Error Response
**CWE-209 · CVSS 7.5 (High) · A10**

A malformed bulk lookup returned `500` with a stack trace (`/app/server.js:809`) and an internal block containing the database connection string. Errors leak internals instead of failing generically.

### AV-11: Dependency Confusion in Mod Install
**CWE-427 · CVSS 8.6 (High) · A03**

A public mod registry entry (`av-leaderboard@9.9.9`), published without sign-in, was installed in place of the internal package because the install logic selects the highest version available. This is a supply chain attack pattern.

### AV-12: MFA Not Enforced on Admin Session
**CWE-308, CWE-287 · CVSS 8.1 (High) · A07**

The login displayed an MFA prompt, but the session kept admin rights. `GET /api/admin/console-token` returned `200 OK` with a console token, showing that the server never enforces second-factor status.

---

## 💡 Key Takeaways

- Enforce authorisation on the server for every endpoint, not only in the user interface.
- Never return secrets in responses or logs, and rotate any that were exposed.
- Store passwords with a slow, salted hash, and enforce MFA server-side.
- Encode output and harden XML parsers and error handling.

## 🧰 Skills Demonstrated

- Web Application Penetration Testing
- OWASP Top 10 Analysis
- Burp Suite
- REST API Security Testing
- CVSS 3.1 Risk Assessment
- CWE Classification
- Vulnerability Analysis
- Technical Security Reporting

---

## Disclaimer

Testing was performed against a deliberately vulnerable local training lab as part of a university course. No real systems, customers, or production data were involved.

Flags, credentials, and personal data are intentionally omitted.
