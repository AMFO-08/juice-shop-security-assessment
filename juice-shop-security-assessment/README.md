# OWASP Juice Shop — Web Application Security Assessment

## Overview

This project documents my hands-on security testing of **OWASP Juice Shop**, an intentionally vulnerable web application built for security training.

I worked through selected challenges, investigated how certain vulnerabilities behaved, and documented what I was able to confirm. I also included practical ways developers can prevent similar issues.

The focus was on understanding the vulnerabilities, testing them in a local environment, and recording the results without claiming more than I actually verified.

## Project Details

| Attribute     | Details                                                                        |
| ------------- | ------------------------------------------------------------------------------ |
| Project Type  | Web Application Security Assessment                                            |
| Target        | OWASP Juice Shop                                                               |
| Environment   | Kali Linux, Docker, and a web browser                                          |
| Testing Scope | Selected application security challenges                                       |
| Primary Focus | Vulnerability testing, access control, SQL injection, and client-side security |
| Documentation | Markdown                                                                       |
| Screenshots   | Not included                                                                   |

## Objectives

* Practise identifying and validating web application vulnerabilities.
* Understand how insecure application behavior can be exploited.
* Inspect HTTP requests and responses using browser developer tools.
* Investigate access control weaknesses and privilege escalation.
* Explore SQL injection and client-side vulnerabilities.
* Understand the risks of exposed credentials and confidential information.
* Document findings and recommend practical fixes.
* Build my hands-on web application penetration-testing skills.

## Findings

I documented nine findings from the assessment.

| # | Finding                                                                      | Summary                                                                                                             |
| - | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 1 | [SQL Injection](findings/sql-injection.md)                                   | Manipulated product-search input altered query behavior and exposed database schema information.                    |
| 2 | [Broken Access Control](findings/broken-access-control.md)                   | An authenticated customer retrieved user records beyond those belonging to their own account.                       |
| 3 | [Privilege Escalation](findings/privilege-escalation.md)                     | Client-controlled role data was accepted when creating an administrator account.                                    |
| 4 | [Unauthorized Basket Access](findings/unauthorized-basket-access.md)         | A customer retrieved another user's basket through an application programming interface (API).                      |
| 5 | [Confidential Document Exposure](findings/confidential-document-exposure.md) | A document containing confidential acquisition information was accessible through the application's file directory. |
| 6 | [Hardcoded Credentials](findings/hardcoded-credentials.md)                   | Testing credentials were found in client-side JavaScript and successfully used in the local lab.                    |
| 7 | [Forged Feedback Attribution](findings/forged-feedback-attribution.md)       | A feedback request accepted a user identifier supplied by the client.                                               |
| 8 | [Forged Review Attribution](findings/forged-review-attribution.md)           | A product review accepted an author identity supplied by the requester.                                             |
| 9 | [DOM-Based Cross-Site Scripting](findings/dom-based-xss.md)                  | Crafted input triggered JavaScript execution through a vulnerable client-side processing path.                      |

The SQL injection testing also exposed database schema information. I documented that as part of the SQL injection finding rather than treating it as a separate vulnerability.

### Other Challenges Explored

* **Password Hash Leak:** The challenge was reported as solved during testing, but I did not retain enough technical evidence to document it properly. I have left it out of the confirmed findings until I can verify the details.
* **Bonus Payload:** I completed this additional DOM-based Cross-Site Scripting exercise. I have not counted it as a separate vulnerability.
* **Stored Cross-Site Scripting:** I tested review input, but the markup remained visible as literal text rather than executing after a page refresh. I could not confirm Stored Cross-Site Scripting during that test.

## What I Learned

### 1. Access Control

Access control problems can allow users to access resources or perform actions that should be restricted to them.

My testing covered unauthorized user-record access, another user's basket, and the ability to manipulate feedback and review attribution.

**What can help prevent these issues:**

* Check authorization on the server for every protected request.
* Verify that a user is allowed to access the specific resource being requested.
* Derive user identity from trusted authentication context.
* Never treat a client-supplied identifier as proof of ownership.
* Apply least privilege and test access controls across different users and roles.

### 2. Privilege Escalation

An application should never allow users to grant themselves privileges simply by changing data in a request.

In the local lab, I was able to create an account with administrator privileges using client-controlled role data.

**What can help prevent this:**

* Assign privileged roles through trusted server-side logic.
* Reject unauthorized changes to sensitive fields.
* Restrict administrative actions to authorized users.
* Test for mass-assignment and privilege-escalation weaknesses.

### 3. SQL Injection

SQL injection happens when untrusted input changes the structure or behavior of a database query.

I tested the product-search functionality and observed that crafted input could change the returned results. Further testing exposed database schema information.

**What can help prevent this:**

* Use parameterized queries or prepared statements.
* Avoid building SQL queries through string concatenation with untrusted input.
* Validate input according to expected formats and data types.
* Limit database account permissions.
* Return safe error messages to clients and keep diagnostic details in protected logs.
* Include SQL injection tests in development and regression testing.

### 4. Sensitive Information and Credentials

Confidential documents and credentials can become security risks when they are exposed in public directories or client-side code.

During testing, I found an accessible confidential document and identified testing credentials embedded in JavaScript.

**What can help prevent this:**

* Keep confidential files outside publicly accessible directories unless access is intentionally restricted.
* Avoid placing passwords, API keys, and other secrets in client-side code.
* Store secrets securely on the server.
* Rotate credentials that have been exposed.
* Limit the information returned by APIs to what the requester actually needs.
* Check deployment files and directories for unintended disclosures.

### 5. Feedback and Review Attribution

An application should not blindly trust identity information supplied by the client when creating records.

My tests showed that feedback and review requests accepted attribution values controlled by the requester. This demonstrated a weakness in how those requests handled identity information.

**What can help prevent this:**

* Derive the submitting user's identity from trusted server-side authentication context.
* Reject or ignore unauthorized client-supplied identity fields.
* Check permissions before creating or modifying records.
* Add appropriate abuse-prevention controls.
* Log relevant security events where useful.

### 6. DOM-Based Cross-Site Scripting

Cross-Site Scripting (XSS) occurs when untrusted input is handled in a way that allows executable content to run in a user's browser.

I completed the DOM-based XSS challenge by supplying crafted input that triggered JavaScript execution in the browser.

**What can help prevent this:**

* Avoid passing untrusted input into unsafe HTML or JavaScript execution contexts.
* Use safe DOM APIs, such as JavaScript's `textContent`, when displaying plain text.
* Apply context-appropriate output encoding.
* Use a maintained sanitization library when rendering HTML is necessary.
* Use a restrictive Content Security Policy (CSP) as an additional layer of defense.
* Test client-side input handling and rendering paths.

## Testing Approach

My general workflow was to:

1. Run OWASP Juice Shop locally.
2. Explore selected application functionality and challenges.
3. Inspect relevant requests and responses using browser developer tools.
4. Change request parameters or input values to investigate application behavior.
5. Check whether the observed behavior demonstrated a security weakness.
6. Document the result, potential impact, and possible remediation.
7. Record limitations where I could not fully verify a claim.

The individual reports contain the details of each finding, including reproduction information and observed behavior where available.

## Tools and Technologies

* **Kali Linux** — Testing environment.
* **Docker** — Running the local application.
* **OWASP Juice Shop** — Intentionally vulnerable web application.
* **Browser Developer Tools** — Inspecting requests, responses, and client-side behavior.
* **HTTP and REST API testing** — Investigating application endpoints.
* **Markdown** — Writing and organizing the documentation.

## Repository Structure

```text
juice-shop-security-assessment/
├── README.md
├── findings/
│   ├── sql-injection.md
│   ├── broken-access-control.md
│   ├── privilege-escalation.md
│   ├── unauthorized-basket-access.md
│   ├── confidential-document-exposure.md
│   ├── hardcoded-credentials.md
│   ├── forged-feedback-attribution.md
│   ├── forged-review-attribution.md
│   └── dom-based-xss.md
└── report/
    └── security-assessment-report.md
```

## Scope and Limitations

This assessment was performed against OWASP Juice Shop in a local training environment.

* Testing covered selected challenges, not every feature of the application.
* Findings are based on behavior observed during testing.
* Supporting request and response evidence was not retained for every challenge.
* The Password Hash Leak challenge requires further technical verification before it can be documented as a confirmed finding.
* Stored Cross-Site Scripting was explored but not confirmed.
* Screenshots are not included in this repository.
* Formal severity ratings and comprehensive remediation retesting have not been completed for every finding.
* The underlying source code was not inspected for every finding, so some root causes remain unverified.

These limitations matter because a finding should be supported by evidence, and the observed behavior should not be used to claim impacts that were never tested.

## Key Takeaways

This project helped me understand how common web application security weaknesses can affect confidentiality, integrity, and access control.

A few things stood out to me:

* Authorization needs to be enforced on the server, not trusted to the client.
* User input should never be allowed to change the intended structure of a database query.
* Credentials and confidential information need to be protected from unintended exposure.
* Client-controlled identity fields should not determine who owns or submits a record.
* Unsafe handling of browser input can lead to JavaScript execution.
* Findings should explain what was actually tested, what happened, and what still needs verification.

I'm continuing to build on these practical exercises as I learn more about web application security and penetration testing.

## Disclaimer

This project is intended for educational purposes and defensive security learning.

All testing documented here was performed against a deliberately vulnerable application in a local training environment. Security testing should only be performed on systems for which explicit authorization has been obtained.
