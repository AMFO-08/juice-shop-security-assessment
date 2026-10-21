# Unauthorized Basket Access

## What I Found

While testing the basket functionality in OWASP Juice Shop, I checked whether a customer could access a basket belonging to another user by changing the basket ID in an API request.

I found that a customer session could retrieve a basket associated with a different user.

The endpoint I tested was:

`GET /rest/basket/1`

The response returned HTTP 200 and included the basket's user ID, product information, and quantities.

This confirmed an access control weakness in the basket functionality of my local training environment.

## How I Tested It

I used my authenticated customer session to interact with the basket API.

First, I added a product to a basket through the API. I then requested basket ID `1` and examined the response.

The request succeeded and returned a basket object associated with user ID `1`, rather than restricting the response to the basket belonging to my customer account.

The key observation was that the server returned the requested basket without adequately restricting access based on ownership.

## Why This Matters

A shopping basket can contain information about a user's shopping activity, including products they intend to purchase and the quantities selected.

If an application allows customers to access other users' baskets, it may expose private shopping information.

Depending on the operations supported by the API, similar authorization weaknesses could potentially allow unauthorized users to modify another person's basket as well.

However, **my test confirmed unauthorized basket retrieval, not unauthorized modification of another user's basket**. That would need to be investigated separately.

## How I'd Fix It

The server should verify that the authenticated user owns the requested basket or otherwise has explicit permission to access it.

I'd recommend:

* Checking basket ownership on every request that retrieves or modifies a basket.
* Deriving the requesting user's identity from trusted server-side authentication context.
* Never treating a basket ID supplied by the client as proof of ownership.
* Returning an appropriate authorization error when a user requests a basket they cannot access.
* Applying the same checks to adding, updating, and removing basket items.
* Testing basket access with multiple accounts to ensure one customer cannot access another customer's data.

Changing the basket ID in the browser or API request should never be enough to gain access to another user's basket.

## How I'd Verify the Fix

After implementing the fix, I'd test basket access using two separate customer accounts.

I'd verify that each customer can access their own basket but receives an appropriate error when requesting the other customer's basket.

I'd also test whether unauthorized users can add, change, or remove items in someone else's basket.

Those additional tests would help confirm that the ownership checks apply to both reading and modifying basket data.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed unauthorized basket retrieval through the API response, but I did not inspect the source code to identify the exact implementation defect.

The test did not establish whether other basket operations were vulnerable, and I haven't assigned a formal severity rating.

## Takeaway

This test showed why APIs must check resource ownership instead of trusting identifiers supplied by the client.

Even when a user is properly authenticated, the application still needs to verify that they are allowed to access the specific basket they requested.
