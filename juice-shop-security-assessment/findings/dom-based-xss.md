# DOM-Based Cross-Site Scripting (XSS)

## What I Found

While working through the OWASP Juice Shop challenges, I tested how the application handled input entered into its search field.

I discovered that a crafted HTML iframe payload could trigger JavaScript execution in the browser. This demonstrated a DOM-based Cross-Site Scripting (XSS) issue in the application's client-side input handling.

The payload I used for the challenge was:

```html
<iframe src="javascript:alert(`xss`)">
```

The payload executed in my local training environment, and I completed the associated challenge.

## How I Tested It

I entered the crafted payload into the application's search field and observed the browser's behavior.

Instead of treating the input strictly as search text, the application processed it in a way that allowed JavaScript to execute in the page.

This confirmed that the tested client-side input path was vulnerable to DOM-based XSS.

I also completed the Bonus Payload challenge using a separate payload supplied by the training challenge. I'm treating that as an additional exercise within the same general area, rather than counting it as a separate confirmed vulnerability.

## Why This Matters

Cross-Site Scripting occurs when an application allows untrusted content to be interpreted as executable code in a user's browser.

Depending on the page's context and the application's security controls, XSS may allow an attacker to manipulate page content, perform actions within a victim's authenticated session, or access information available to scripts running in that context.

The actual impact depends on the application's behavior, the affected page, and other security controls.

My test confirmed JavaScript execution through the vulnerable input path. I did not establish that the payload could access every type of user information or bypass all browser security controls.

## How I'd Fix It

I'd start by reviewing how the application processes search input and inserts it into the Document Object Model (DOM).

The application should treat user input as data rather than executable HTML or JavaScript.

I'd recommend:

* Avoiding unsafe DOM operations that interpret untrusted input as HTML.
* Using safe DOM APIs such as `textContent` when displaying plain text.
* Applying context-appropriate output encoding wherever user-controlled data is rendered.
* Using a well-maintained sanitization library if rendering user-supplied HTML is genuinely necessary.
* Applying a restrictive Content Security Policy (CSP) as an additional layer of defense.
* Testing client-side input handling with crafted payloads during security testing.

A Content Security Policy can reduce the impact of some XSS attacks, but it should not replace fixing the vulnerable input-handling behavior itself.

## How I'd Verify the Fix

After implementing a fix, I'd repeat the original test in the local training environment.

I'd verify that the payload is displayed as harmless text or safely rejected and that it no longer causes JavaScript execution.

I'd also test other input paths that feed data into the DOM to check whether the same weakness exists elsewhere.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed JavaScript execution using the challenge payload. I did not inspect the source code to identify the exact unsafe DOM operation responsible.

Stored XSS was investigated separately, but my attempts to make review content execute JavaScript after refreshing the page were unsuccessful. I therefore have not confirmed Stored XSS as part of this finding.

No formal severity rating has been assigned.

## Takeaway

This challenge helped me understand that XSS is not limited to scripts embedded in server-generated pages. Client-side JavaScript can introduce the same type of risk if it processes untrusted input unsafely.

The key lesson is to handle user input safely throughout the application and avoid interpreting untrusted data as executable content.
