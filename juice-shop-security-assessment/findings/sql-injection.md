# SQL Injection in Product Search

## What I Found

While testing the product-search functionality in OWASP Juice Shop, I discovered that specially crafted search input could change how the application queried its database.

The endpoint I tested was:

`GET /rest/products/search?q=...`

A normal search worked as expected, but things got interesting when I started testing how the endpoint handled unusual input. I was able to manipulate the search results and eventually retrieve database schema information.

This confirmed that the product-search functionality was vulnerable to SQL injection in my local training environment.

## How I Tested It

I started by comparing the results from normal searches with the responses to unusual search input.

Here are the main things I observed:

* **Normal search:** Searching for `apple` returned matching products.
* **Malformed input:** A single-quote input returned no products, while one malformed injection attempt produced an HTTP 500 error containing a SQLite error message.
* **Manipulated query conditions:** I tested conditions that evaluated as true and false. The true condition returned a much broader collection of products, while the false condition returned no results.
* **Database schema:** I went further and tested whether the query could expose database structure. I was able to retrieve schema definitions from SQLite's metadata, completing the Database Schema challenge.

The important part wasn't just getting unexpected results. It was seeing how changing the input changed the database query's behavior.

## Why This Matters

Product search might seem like a relatively harmless feature, but if user input can alter a database query, the consequences can extend beyond the search results.

Depending on the vulnerable query and the database permissions, SQL injection could allow an attacker to retrieve information they shouldn't have access to.

In my test, I confirmed two things: the search results could be manipulated, and database schema information could be exposed.

I did not establish that this particular vulnerability allowed arbitrary database modification or operating-system command execution, so I'm not claiming those impacts.

## How I'd Fix It

The main fix is to use **parameterized queries or prepared statements**. Search input should be treated as data, not as part of the SQL instructions being executed.

I'd also recommend the following:

* Validate search input against the application's expected format and length.
* Give the database account only the permissions it actually needs.
* Avoid returning detailed database errors to users. Log diagnostic information securely on the server instead.
* Review other endpoints that accept user input and interact with the database.
* Add automated tests to make sure injection-style input cannot change the intended query behavior.

Input validation is useful, but it shouldn't be treated as a replacement for parameterized queries.

## How I'd Verify the Fix

After implementing the fix, I'd repeat the same tests in an authorized environment.

I'd check that normal searches still work, crafted input no longer changes the query logic, and database schema information cannot be retrieved through the search endpoint.

I'd also check that malformed input doesn't expose internal database errors to the client.

## Notes and Limitations

This testing was performed against my local OWASP Juice Shop training environment running in Kali Linux with Docker.

I confirmed the vulnerability through the application's behavior, but I didn't inspect the source code to identify the exact SQL statement responsible. The underlying implementation issue therefore remains unverified.

The testing was limited to the product-search functionality, and I haven't assigned a formal severity rating.

## Takeaway

This was a useful demonstration of why applications must handle database input carefully. A search field shouldn't be able to change the instructions sent to a database.

For me, the key lesson was to compare normal behavior with unexpected behavior, validate what the application actually returns, and recommend a fix that addresses the underlying weakness rather than just blocking a few suspicious characters.
