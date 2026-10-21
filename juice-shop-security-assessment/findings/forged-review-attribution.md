# Forged Review Attribution

## What I Found

While testing the product-review functionality in OWASP Juice Shop, I checked whether the application verified the identity attached to a submitted review.

I found that the review request included an `author` field supplied by the client. I tested this by submitting a review with a different user's email address in that field.

The application accepted the request and returned a success response.

This demonstrated that the client could influence the displayed author of a product review in my local training environment.

## How I Tested It

I tested the review functionality for a product using this endpoint:

`PUT /rest/products/1/reviews`

The request body included two fields:

* `message` — the review text.
* `author` — the author value supplied by the client.

I submitted a harmless test review and set the `author` field to `admin@juice-sh.op`.

The application returned HTTP 201 Created with a response indicating success.

This confirmed that the endpoint accepted the submitted review with the client-supplied author value. I did not independently verify every place where that value was subsequently displayed.

## Why This Matters

Product reviews can influence purchasing decisions, and users generally expect reviews to be attributed accurately.

If an application allows someone to submit a review under another person's identity, the integrity of its reviews can be undermined.

Depending on how the application uses the author field, this could allow misleading reviews to appear associated with another user or a privileged account.

My test demonstrated that the client could supply the author value and that the submission was accepted. The full downstream impact would require further testing.

## How I'd Fix It

The server should establish review authorship through trusted application logic rather than relying on a client-supplied author field.

I'd recommend:

* Deriving the author identity from the authenticated user's session when reviews require authentication.
* Ignoring or rejecting unauthorized client-supplied author values.
* Validating review submissions on the server.
* Enforcing the intended permissions for creating, editing, and deleting reviews.
* Keeping review attribution consistent between the stored record and the information displayed to other users.
* Adding tests to verify that one user cannot impersonate another when submitting reviews.

If anonymous reviews are supported, they should follow a clearly defined design that doesn't let a requester falsely claim another user's identity.

## How I'd Verify the Fix

After implementing the fix, I'd submit a review while authenticated as a test user and attempt to specify another user's identity in the request.

I'd verify that the application either derives the author from the authenticated session or rejects the unauthorized override.

I'd then inspect the resulting review to confirm that its displayed attribution matches the identity established by the server.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed that the review endpoint accepted a request containing a client-supplied author value and returned a success response.

I did not inspect the source code or verify every downstream use of the author field. No formal severity rating has been assigned.

## Takeaway

This test showed why applications should not trust a client to decide who authored a record.

The server should establish review attribution through trusted logic so users cannot simply change an identity field in their request and claim to be someone else.
