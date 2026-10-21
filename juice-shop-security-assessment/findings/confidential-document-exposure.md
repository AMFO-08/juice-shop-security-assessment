# Confidential Document Exposure Through Public File Access

## What I Found

While exploring OWASP Juice Shop, I came across a file directory exposed by the application. Inside it, I found a document named `acquisitions.md` that was marked confidential and described planned acquisitions.

The document was accessible through the application's file path:

`/ftp/acquisitions.md`

I followed the file listing and opened the document. After returning to the challenge scoreboard, I saw that the associated challenge had been marked as completed.

This showed that a document labelled confidential was accessible through the application's exposed file directory in my local training environment.

## How I Tested It

I started by checking the application's About page, where I noticed a reference to `ftp/legal.md`. This led me to investigate the `/ftp/` path.

I opened the file listing and located `acquisitions.md`. The document contained information about planned acquisitions and was marked confidential.

I opened the document through the application's file directory and checked the challenge scoreboard afterward. The challenge was then marked as solved.

The important observation was that the application made this document accessible through a path that could be reached from its file listing.

## Why This Matters

Files stored alongside an application's publicly accessible resources can become exposed if access restrictions are not configured properly.

If confidential documents are deployed in a publicly accessible directory, an unauthorized visitor may be able to retrieve them without needing access to the system that originally produced or stored them.

Depending on the information exposed, this could reveal internal plans, business-sensitive information, or other material that was never intended for public access.

In this case, I confirmed access to a document marked confidential. I did not establish that the file contained genuine business information or that any real organization was affected.

## How I'd Fix It

I'd start by checking whether the document actually needs to be accessible to the public.

If it contains confidential information, the safest approach is to remove it from the public directory and store it somewhere that requires appropriate authorization.

I'd also recommend:

* Reviewing deployed files for confidential documents, backups, and other unintended disclosures.
* Keeping sensitive files outside publicly served directories.
* Enforcing server-side authorization when private files must be accessible through the application.
* Reviewing deployment configurations to ensure internal or development files aren't published accidentally.
* Including checks for exposed files in deployment reviews and security testing.
* Avoiding reliance on obscure filenames or file extensions as a security control.

The main lesson is that marking a document as confidential does not protect it. Access restrictions must actually be enforced.

## How I'd Verify the Fix

After applying the fix, I'd attempt to access the document directly and through the application's file listing.

If the document is intended to be private, an unauthorized request should not return its contents. The application should return an appropriate response, such as HTTP 403 Forbidden or HTTP 404 Not Found, depending on its design.

I'd also review the surrounding directory to check whether other unintended files are accessible.

## Notes and Limitations

Testing was performed against my local OWASP Juice Shop training environment.

I confirmed that the document was accessible through the application's file directory and that the corresponding challenge was marked as completed.

I did not inspect the source code or server configuration to establish the exact reason the file was exposed. I also did not establish that the document contained real confidential business information.

The finding is limited to the observed file exposure in the training application.

## Takeaway

This investigation showed why deployment hygiene matters. A document can be exposed simply because it was placed in a directory the application serves publicly.

Sensitive files should be kept out of public directories, and access to private resources should be enforced by the server rather than relying on filenames or assumptions about who will find them.
