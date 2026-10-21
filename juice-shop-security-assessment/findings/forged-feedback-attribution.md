# Forged Feedback Attribution

## What I Found

While testing the feedback functionality in OWASP Juice Shop, I checked whether the application verified the identity associated with a feedback submission.

I found that the feedback API accepted a `UserId` supplied in the request body. I tested this by submitting feedback with a different user ID, and the application accepted the request.

The response returned HTTP 201 Created and included the user ID I had supplied.

This showed that the client could influence how feedback was attributed to a user in my local training environment.

## How I Tested It

I examined the request used to submit feedback through the application.

The request was sent to:

`POST /api/Feedbacks/`

I then submitted a test feedback entry with a client-supplied `UserId` of `1`, along with the other fields required by the endpoint.

The application returned HTTP 201 Created. The response included the submitted user ID and the harmless test comment.

This confirmed that the API accepted the client-supplied identity value rather than preventing the behavior I was testing.

## Why This Matters

Feedback systems may use user identity to track submissions, investigate complaints, or determine who submitted particular information.

If an application trusts a user ID supplied by the client without verifying it, someone may be able to make feedback appear to have come from another account.

This could undermine the reliability of feedback records and allow users to misattribute comments to other people.

My test confirmed that the API accepted the supplied user ID. It did not establish that every feedback-related workflow or administrative function was vulnerable.

## How I'd Fix It

The server should determine the identity associated with a feedback submission using trusted authentication context and the application's intended access-control rules.

I'd recommend:

* Deriving the submitting user's identity from the authenticated session when feedback is associated with a logged-in user.
* Ignoring or rejecting client-supplied identity fields that the requester is not authorized to set.
* Validating the request body against an explicit schema.
* Allowing anonymous feedback only through a deliberately designed workflow that does not falsely attribute submissions to another account.
* Testing feedback submission with authenticated and unauthenticated requests.
* Logging relevant security events where appropriate.

The important point is that the server must control attribution. A client-supplied ID should not, by itself, establish who submitted the feedback.

## How I'd Verify the Fix

After implementing the fix, I'd submit feedback while authenticated as one user and attempt to supply another user's ID.

I'd check that the server either derives the correct identity from the authenticated session or rejects an unauthorized identity override.

I'd also verify that any supported anonymous-feedback workflow continues to function correctly.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed that the feedback endpoint accepted a client-supplied `UserId` and returned it in the response.

I did not inspect the source code to establish the exact implementation defect or investigate every feedback-related workflow. No formal severity rating has been assigned.

## Takeaway

This test reinforced the importance of treating identity fields in client requests as untrusted input.

If a record needs to be associated with a particular user, the application should establish that relationship through trusted server-side logic rather than blindly accepting the identity supplied by the client.
