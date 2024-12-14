What is SSRF?

SSRF occurs when a server-side application makes requests on our behalf. For example, with an SSRF payload we can potentially make an external-facing website make requests to internal resources that we have no direct access to.

Welcome to the Section on Server-side request forgery, an interesting vulnerability that has been steadily becoming more and more prevalent. It's often fairly easy to spot, but can be tricky to demonstrate an impact, so lets take a closer look.

Server-side request forgery occurs when a server-side application makes requests on our behalf. It does this by taking a URL or partial URL from the user and then making a request to that endpoint.

Often, the application will then process the results and then return some data back to the user. 

So here is a more visual representation of SSRF, and we can see that the original request has a JSON payload in the body, and the front-end is, of course, set up so that it passes the URL of an internal endpoint to check the stock of an item. 

However, if this input is trusted without an validation, or if it's not validated properly, as with many vulnerabilities, we can use it to access other internal endpoints.

In this example here, we can see that we're going to start sending requests to internal .local slash admin instead of the stock .internal .com on port 8000

Screenshot: Visual Representation of SSRF:

![[Pasted image 20241213232541.png]]

