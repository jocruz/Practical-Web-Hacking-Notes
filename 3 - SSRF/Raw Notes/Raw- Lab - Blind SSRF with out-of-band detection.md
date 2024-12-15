
**_Lab: Blind SSRF with out-of-band detection

https://portswigger.net/web-security/ssrf/blind/lab-out-of-band-detection

Description: 

This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server.

#### Note

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

Portswigger solution:
Solution:
1. Visit a product, intercept the request in Burp Suite, and send it to Burp Repeater.
2. Go to the Repeater tab. Select the Referer header, right-click and select "Insert Collaborator Payload" to replace the original domain with a Burp Collaborator generated domain. Send the request.
3. Go to the Collaborator tab, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously.
4. You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.

Instructor Lab Notes:

Right off the bat we are told that this is solved using Burp Suite Pro. Since we do not own Burp Suite Pro, we are told that we should just watch and learn as oppose to doing it ourselves because we might one day own it. As for right now, this lab is to serve just as informational.

We are going to be using Collaborator. We going to go ahead and go to the Lab. Go to the Collaborator. 

The instructor goes to the webpage of the lab, we click on view details of one of the items in the webapge and we are brought to a URL that says "/product?productid=1"

From there we can go to Burp Suite and find the request with the Method of GET and the URL is "/product?productId=1"

Something to note is that we have an address in the Referer header. 

Referer:https://0a4e0069004affadb82385b6d003f009.web-security-academy.net/

As soon as we see a request in the headers we should think about testing for SSRF.

If we send that request to Repeater, we can change the URL in the request in repeater which is located in the Referer.

Once we change the URL in the Referer and we send the Request to the Web application using Repeater we see that the web application returns a 200 response which means that the web application does not care what URL we use and does not return a 400 like it should. The data from the referer is not being returned in the HTTP response.


But if we put a collaborator payload (Which is just the payload we can copy from the Collaborator tab) we can see in Collaborator that we do get a return once we copy and paste the payload url into our Referer header (we make sure we add http:// right before the payload).

As we check Collaborator we can see that we do get traffic.

We should think that once we do accomplish the steps above that we do indeed have and proven that we have found SSRF and it can be reported and documented as such with the steps we have done so far.

Okay the instructor claims that if we do not have Burp Suite pro then we can use the shite webhook.site and we can prove we have blind SSRF using the the webhook.site unique URL and see if we can generate traffic as we go along.

When dealing with Blind SSRF proving that we were able to generate traffic can be enough to bring up to Developers and say that this could cause impact. However if we are in a bug bounty we would have to determine if we are in scope or out of scope when dealing with SSRF.



