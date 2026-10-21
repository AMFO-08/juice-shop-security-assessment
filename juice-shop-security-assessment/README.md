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

| # | Finding                                                              | Summary                                                                                          |
| - | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| 1 | [SQL Injection](findings/sql-injection.md)                           | Manipulated product-search input altered query behavior and exposed database schema information. |
| 2 | [Broken Access Control](findings/broken-access-control.md)           | An authenticated customer retrieved user records beyond those belonging to their own account.    |
| 3 | [Privilege Escalation](findings/privilege-escalation.md)             | Client-controlled role data was accepted when creating an administrator account.                 |
| 4 | [Unauthorized Basket Access](findings/unauthorized-basket-access.md) | A customer retrieved another user's basket through an application pro                            |
