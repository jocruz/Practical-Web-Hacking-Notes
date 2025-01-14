Slide 1:

What is an Open Redirect?

Open Redirects occur when an application accepts untrusted input that could cause a redirection to an external URL.

We can exploit this to redirect users to sites that attempt to capture credentials or deliver malware.


Slide2:

1. Users logs in
2. Application forwards the user using a query parameter
	1. https://tcm-sec.com/login?redirectURL=https://tcm-sec.com/dashboard
3. We craft a malicious link and trick the user into clicking it
	1. https://tcm-sec.com/login?redirectURL=https://malicious.com/



instructor transcript:

So let's take a look at OpenRedirects, and this is a fairly straightforward section,
but once again, we need to be on the lookout for this vulnerability in different contexts.
We do have an introduction to OpenRedirects covered in the Practical Bug Bounty Course,
but let's quickly do a quick recap before we jump into a lab.
So OpenRedirect vulnerabilities occur when an application incorporates user-supplied
inputs into the destination of a redirection or URL forward, without properly validating it.
This can allow us to redirect users to malicious websites,
so we can do things like steal credentials or potentially spread malware.
The vulnerability is particularly dangerous because it exploits the trust the users have
in a legitimate website to direct them to the malicious site.
So here's a simple example. Imagine a website, tcmsec.com, that redirects a specified user
after they log in. The URL might be passed through the query parameter, like so.
We have redirect URL equals HTTPS colon slash slash dcmsec.com slash dashboard.
We could craft a malicious link that replaces the legitimate redirect URL with the URL controlled
by us. So in this case, instead of the redirect URL going to dcmsec.com slash dashboard,
it could forward to malicious.com. And this is quite tricky to see, as most people will just
look at the top level domain, so they just see tcmsec, so probably all good, and then go ahead
and click on it. So let's take a look at a guided lab where we have a DOM-based open redirect issue.
All right, so here we are at a DOM-based open redirect lab. And of course,
this lab contains an open redirection vulnerability.
