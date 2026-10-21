# Privilege Escalation Through Client-Controlled Role Assignment

## What I Found

While exploring the user registration API in OWASP Juice Shop, I discovered that the application accepted a role value supplied by the client when creating an account.

I tested this using a normal customer account's authentication token. By including an administrator role in the account-creation request, I was able to create an account with administrator privileges.

I then logged in with the newly created account and accessed the application's administration page.

This confirmed a privilege escalation issue in my local training environment.

## How I Tested It

I started with a normal customer account and examined the account-creation functionality.

I sent a `POST` request to:

`/api/Users/`

In the request body, I included a role value of `admin` rather than relying on the application to assign an appropriate role.

The application accepted the request and created an account with administrator privileges. I then logged in using that account and opened the administration page, where I could view administrative information, including registered users and feedback.

During testing, I created disposable test accounts to validate the behavior.

The important observation was that a normal customer could influence the role assigned to a newly created account through client-controlled input.

## Why This Matters

Administrator accounts generally have access to functionality and information that ordinary users should not be able to access.

If an application lets users assign themselves privileged roles, an attacker could potentially gain administrative access without approval from an authorized administrator.

Depending on the application's permissions, this could expose user information, allow unauthorized management actions, or compromise other parts of the application.

In this test, I confirmed that the created account received administrator privileges and could access the administration page. I haven't established the full range of actions that the account could perform.

## How I'd Fix It

The main fix is to **make role assignment a server-controlled operation**.

The client should not be able to decide whether a newly created account is a customer, administrator, or another privileged role.

I'd recommend:

* Assigning the default customer role on the server during ordinary registration.
* Rejecting or ignoring unauthorized attempts to set privileged fields.
* Allowing role changes only through explicitly authorized administrative operations.
* Validating which fields each endpoint accepts instead of blindly accepting the entire request body.
* Enforcing authorization checks on administrative endpoints, even if the frontend hides them.
* Logging and monitoring sensitive actions such as role changes and administrator account creation.
* Adding automated tests to ensure that a normal customer cannot assign themselves privileged roles.

These controls should be enforced on the server. Simply removing the role field from the registration form would not be sufficient because a client can construct its own API request.

## How I'd Verify the Fix

After implementing the fix, I'd repeat the account-creation test using a normal customer account.

I'd check that supplying an administrator role either gets rejected or has no effect, and that the newly created account receives only the permissions intended for ordinary users.

I'd also test legitimate administrator role-management workflows to make sure authorized changes still work correctly.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed the behavior through the account-creation response and subsequent access to the administration page. I did not inspect the source code to identify the exact implementation defect.

The testing confirms the role-assignment weakness described above, but it does not establish every possible privilege escalation path in the application. No formal severity rating has been assigned.

## Takeaway

This was a good example of why applications shouldn't trust client-supplied privilege information.

A user can modify the requests sent by their browser, so the server must decide which roles can be assigned and verify that the requester has permission to perform privileged actions.
