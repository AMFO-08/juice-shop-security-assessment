# OWASP Juice Shop — Security Assessment Report

## 1. Overview

This report documents my hands-on security assessment of OWASP Juice Shop, a deliberately vulnerable web application designed for security training.

I used the application to practise testing web functionality, investigating API behavior, identifying security weaknesses, and understanding how those weaknesses could affect an application.

The assessment covered issues involving access control, SQL injection, exposed information, identity attribution, and client-side JavaScript execution.

Alongside identifying the issues, I documented possible fixes to understand how developers can prevent similar problems.

## 2. Scope and Environment

### Target

OWASP Juice Shop running locally in my training environment.

### Environment and Tools

* Kali Linux
* Docker
* Web browser and Developer Tools
* HTTP requests and REST API testing
* JavaScript inspection through browser developer tools
* Markdown for documenting the findings

### Scope

Testing focused on selected Juice Shop challenges and related application behavior.

I examined API endpoints, tested how the application handled user input, checked whether resources were properly protected, and investigated information exposed through client-side code and publicly accessible files.

This was a limited training assessment, not a comprehensive review of every feature in the application.

## 3. Summary of Findings

I documented nine findings from the testing.

| # | Finding                                                                         | Main Security Concern                                                                              |
| - | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| 1 | [SQL Injection in Product Search](../findings/sql-injection.md)                 | User input could alter database query behavior and expose schema information.                      |
| 2 | [Broken Access Control — User Records](../findings/broken-access-control.md)    | An ordinary customer could retrieve records belonging to other users.                              |
| 3 | [Privilege Escalation](../findings/privilege-escalation.md)                     | Client-controlled role assignment allowed an ordinary customer to create an administrator account. |
| 4 | [Unauthorized Basket Access](../findings/unauthorized-basket-access.md)         | A customer could retrieve another user's basket.                                                   |
| 5 | [Confidential Document Exposure](../findings/confidential-document-exposure.md) | A document marked confidential was accessible through the application's file directory.            |
| 6 | [Hardcoded Credentials](../findings/hardcoded-credentials.md)                   | Testing credentials were exposed in client-side JavaScript and could be used to log in.            |
| 7 | [Forged Feedback Attribution](../findings/forged-feedback-attribution.md)       | The feedback API accepted a client-supplied user ID.                                               |
| 8 | [Forged Review Attribution](../findings/forged-review-attribution.md)           | A review submission accepted a client-supplied author identity.                                    |
| 9 | [DOM-Based Cross-Site Scripting](../findings/dom-based-xss.md)                  | Crafted input triggered JavaScript execution in the browser.                                       |

The findings were identified in an intentionally vulnerable local application. They should not be interpreted as evidence that a production system has the same weaknesses.

No formal severity ratings were assigned during this assessment.

## 4. Testing Approach

I approached the assessment by exploring the application's functionality and checking whether its behavior matched what I would expect from a secure application.

My testing included the following activities.

### Access Control Testing

I used a normal customer session to investigate whether the application restricted access to user records and baskets belonging to other users.

I also tested whether client-controlled role information could affect account privileges.

### Input and Database Testing

I compared normal product-search requests with requests containing unusual or specially crafted input.

This helped me identify SQL injection behavior and investigate whether database schema information could be retrieved.

### Information Exposure

I inspected client-side JavaScript and explored the application's file directory to check whether credentials or other information were unnecessarily exposed.

### Identity Attribution

I tested the feedback and review functionality to see whether the application trusted identity information supplied by the client.

### Client-Side Security Testing

I investigated how the search functionality processed crafted input and confirmed JavaScript execution through a DOM-based XSS challenge.

For each finding, I documented the behavior I observed and considered the potential impact and appropriate remediation.

## 5. Key Security Observations

Several findings highlighted important security principles.

### 5.1 Authentication Does Not Guarantee Authorization

The user-record and basket tests demonstrated that an authenticated user could access resources beyond their own.

An application must check not only whether a requester is logged in, but also whether that requester is authorized to access the particular resource.

Authorization should be enforced on the server for every relevant operation.

### 5.2 Privileged Actions Must Be Controlled by the Server

The privilege escalation test showed that client-controlled role information could influence the permissions assigned to a new account.

Users should not be able to grant themselves administrator privileges through a modified API request.

Role assignment and other privileged operations must be restricted to trusted server-side logic and appropriately authorized users.

### 5.3 User Input Must Not Control Database Query Structure

The SQL injection testing demonstrated that crafted search input could alter query behavior and expose database schema information.

Parameterized queries are the primary recommended defense because they separate data from SQL instructions.

### 5.4 Information Exposure Can Create Additional Risks

The exposed document and hardcoded credentials demonstrated two different information exposure problems.

Files containing confidential information should not be placed in publicly accessible directories, and credentials should not be embedded in client-side code.

Information that reaches a user's browser or is served through a public path should not be assumed to remain secret.

### 5.5 User Identity Must Be Established Reliably

The feedback and review tests demonstrated that client-supplied identity values could influence attribution.

The server should establish authorship and ownership through trusted application logic rather than blindly accepting identity fields from a request.

### 5.6 Client-Side Input Handling Needs Appropriate Protection

The DOM-based XSS test demonstrated that unsafe handling of input can result in JavaScript execution in the browser.

Applications should use safe DOM APIs, appropriate output encoding, and sanitization where rendering user-supplied HTML is necessary.

A Content Security Policy can provide additional protection, but it does not replace fixing the underlying vulnerability.

## 6. Recommended Priorities

Based on the behavior observed, I would investigate and address the following areas first.

These are remediation priorities for the training application, not formal severity ratings.

### Priority 1: Review Access Control and Privilege Assignment

* Prevent ordinary customers from accessing other users' private records and baskets.
* Enforce authorization checks on protected API endpoints.
* Prevent users from assigning themselves privileged roles.
* Test administrative functions with accounts that have different permissions.

These issues directly concern the boundaries between ordinary and privileged users.

### Priority 2: Fix SQL Injection

* Replace unsafe query construction with parameterized queries or prepared statements.
* Review other database-backed endpoints for similar weaknesses.
* Restrict database permissions.
* Avoid returning detailed database errors to clients.
* Add regression tests for injection-style input.

### Priority 3: Remove Exposed Credentials and Confidential Files

* Remove credentials from client-side JavaScript.
* Rotate exposed credentials that remain valid.
* Move confidential documents out of public directories.
* Review deployed files and build artifacts for accidental exposure.

### Priority 4: Correct Identity Attribution

* Derive feedback and review authorship from trusted server-side context.
* Reject unauthorized identity overrides.
* Test record creation and modification with multiple accounts.

### Priority 5: Fix Client-Side XSS

* Avoid interpreting untrusted input as executable HTML or JavaScript.
* Use safe DOM APIs and context-appropriate output encoding.
* Add tests for vulnerable input-handling paths.
* Use a restrictive Content Security Policy as an additional security layer.

## 7. Limitations

This assessment has several limitations:

* Testing was limited to selected OWASP Juice Shop challenges and application functionality.
* The application was tested in a local training environment.
* Source-code analysis was not performed for every finding.
* Supporting request and response evidence was not retained for every challenge.
* The testing did not establish the full impact of every vulnerability.
* Stored XSS was investigated but was not confirmed.
* The Password Hash Leak challenge was left out because its technical details had not been sufficiently verified for an accurate finding report.
* The assessment did not include comprehensive regression testing after remediation because fixes were not implemented and retested as part of this work.
* Formal severity ratings were not assigned.

The individual finding documents describe the specific observations and limitations associated with each issue.

## 8. Conclusion

This project gave me practical experience investigating web application security weaknesses and examining how they affect access control, data protection, and application behavior.

One of the main lessons was that a feature working as intended from the user's perspective does not necessarily mean it is secure. An API can return a successful response while still allowing unauthorized access, and client-side code can expose information or process input in unsafe ways.

The testing also reinforced the importance of verifying observations rather than making assumptions about what a vulnerability allows an attacker to do.

For each finding, I documented the behavior I observed and recommended practical measures that could help prevent similar issues.

This assessment represents my hands-on learning with OWASP Juice Shop and my continued development in web application security and penetration testing.

## Disclaimer

This project was conducted against OWASP Juice Shop in a local training environment for educational purposes.

It documents selected security challenges and does not represent a comprehensive security assessment of every component of the application.

Security testing should only be performed against systems for which appropriate authorization has been obtained.
