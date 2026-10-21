# Broken Access Control: Exposure of User Records

## What I Found

I wanted to see how well the user API restricted access to account information, so I tested it using a normal customer account.

The results surprised me. Even though I was logged in as an ordinary customer, I could retrieve the user collection and access an individual record belonging to the administrator.

The two endpoints I tested were:

* `GET /api/Users`
* `GET /api/Users/1`

Both returned HTTP 200 responses when I made the requests with my customer session.

This confirmed a broken access control issue in the local Juice Shop application.

## How I Tested It

I started with `GET /api/Users`, using the authentication token associated with my customer account.

Instead of returning only information I was permitted to access, the endpoint returned a collection containing records belonging to other users, including the administrator.

I then tested `GET /api/Users/1` with the same customer session. It returned the administrator's record in the test environment.

For comparison, I sent a request to `GET /api/Users` without authentication. That request returned HTTP 401 Unauthorized.

This comparison was useful because it showed that the endpoint required authentication, but that alone wasn't enough to protect the data. The application was identifying whether I was logged in without adequately restricting which user records I could retrieve.

In the responses I examined, password fields and TOTP secrets were not included. However, other user information was still exposed beyond what my customer account should have been allowed to access.

## Why This Matters

An application can require users to log in and still have broken access control.

If an ordinary customer can retrieve other users' records simply by changing an API request, the application may expose information that should remain private.

Depending on the data returned, this could lead to privacy violations, unauthorized access to account information, or information gathering that supports further attacks.

The issue here wasn't that I managed to bypass the login process. **I was already authenticated, but the application didn't properly limit what I could access.**

That distinction matters when investigating access control vulnerabilities.

## How I'd Fix It

I'd start by reviewing the authorization checks on both endpoints.

For `GET /api/Users`, the application should not return the entire user collection to an ordinary customer unless that access is explicitly required and authorized.

For `GET /api/Users/1`, the server should check whether the requesting user has permission to view the requested record. Knowing or guessing a user's ID should not grant access to their information.

I'd also recommend:

* Enforcing authorization checks on the server for every protected API request.
* Returning only the fields needed for the requested operation.
* Restricting user-management functionality to appropriately authorized roles.
* Applying the principle of least privilege.
* Testing API access with different accounts and roles, not just an administrator account.
* Adding automated tests to catch unauthorized access to other users' records.

Hiding a button in the frontend wouldn't solve this issue. The server must enforce the permissions regardless of how a request is made.

## How I'd Verify the Fix

After the changes, I'd repeat the same requests using an ordinary customer account.

I'd expect the full user collection to be restricted to appropriately authorized users and access to another customer's private record to be denied unless explicitly permitted.

I'd also test the administrator's legitimate workflows to make sure the fix doesn't accidentally break authorized user-management functionality.

Finally, I'd check that API responses contain only the information necessary for each operation.

## Notes and Limitations

All testing was performed against my local OWASP Juice Shop training environment.

I confirmed the behavior through the API responses, but I didn't inspect the source code to determine exactly where the authorization checks were missing or incorrectly implemented.

The finding covers the endpoints I tested, not every endpoint in the application. I haven't assigned a formal severity rating.

## Takeaway

This test reinforced an important lesson for me: authentication and authorization solve different problems.

Logging in establishes a user's identity, but the application must still decide what that user is allowed to do. Every protected endpoint needs to enforce that decision on the server, rather than assuming that an authenticated request is automatically authorized.
