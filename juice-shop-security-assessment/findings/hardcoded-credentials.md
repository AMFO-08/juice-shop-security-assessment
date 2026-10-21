# Hardcoded Credentials in Client-Side JavaScript

## What I Found

While exploring the client-side JavaScript in OWASP Juice Shop, I found a username and password defined directly in the application's code.

The credentials were associated with a testing account. I tried them through the application's login page, and the login succeeded.

This confirmed that credentials embedded in the client-side code could be recovered and used to authenticate to the training application.

## How I Tested It

I inspected the JavaScript delivered by the application and noticed variables containing a testing username and password.

Rather than assuming they were valid, I tested the credentials through the application's normal login functionality.

The application accepted them, and the associated challenge was marked as solved.

This demonstrated two things: the credentials were visible to anyone able to inspect the relevant client-side code, and they were still usable for authentication.

I have not included the actual password in this report because reproducing the finding does not require publishing the credential itself.

## Why This Matters

JavaScript delivered to a browser is available for inspection by the person using that browser. Obfuscating the code doesn't make a password or secret stored inside it safe.

If usable credentials are embedded in client-side code, someone may recover them and attempt to access the associated account.

The potential impact depends on the account's permissions, the systems it can access, and whether the credentials are reused elsewhere.

In this lab, I confirmed that the credentials worked for the testing account. I did not establish that they provided access to any external or production system.

## How I'd Fix It

The main fix is to remove credentials from client-side code and ensure that authentication relies on appropriately managed server-side controls.

I'd recommend:

* Removing hardcoded passwords, API secrets, and other sensitive credentials from client-side code.
* Rotating any credentials that have been exposed and could still be used.
* Keeping server-side secrets in an appropriate secret-management system or protected environment configuration.
* Using dedicated, least-privileged test accounts for development and automated testing.
* Preventing test credentials from being included in production builds.
* Reviewing client-side bundles and deployment artifacts for accidentally exposed secrets.
* Monitoring the use of exposed credentials where appropriate.

Simply deleting the credentials from the current source file may not be enough if they remain in deployed bundles, build artifacts, or version-control history. Any credential that was exposed and remains valid should be treated as compromised.

## How I'd Verify the Fix

After remediation, I'd inspect the application's client-side files to confirm that the credentials are no longer present.

I'd also verify that the exposed credentials have been disabled or rotated and that the application continues to authenticate legitimate users correctly.

If the application requires test accounts, I'd check that they are properly restricted and unavailable for unauthorized use in production.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed that testing credentials were present in client-side JavaScript and that they successfully authenticated to the lab application.

I did not establish that the credentials were valid outside the training environment or that they exposed a production system. No formal severity rating has been assigned.

## Takeaway

This was a straightforward but important lesson: anything shipped to the browser should be considered visible to the user.

Credentials and other secrets should never depend on being hidden inside client-side code. They must be protected through appropriate storage, access controls, and credential management.
