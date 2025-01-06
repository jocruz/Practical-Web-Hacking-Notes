
# Lab: Exploiting XInclude to retrieve files

PRACTITIONER

This lab has a "Check stock" feature that embeds the user input inside a server-side XML document that is subsequently parsed.

Because you don't control the entire XML document you can't define a DTD to launch a classic XXE attack.

To solve the lab, inject an `XInclude` statement to retrieve the contents of the `/etc/passwd` file.


Hint:
By default, `XInclude` will try to parse the included document as XML. Since `/etc/passwd` isn't valid XML, you will need to add an extra attribute to the `XInclude` directive to change this behavior.

Solution:
- Visit a product page, click "Check stock", and intercept the resulting POST request in Burp Suite.
- Set the value of the `productId` parameter to:
    
    `<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="f`





Instructor Notes:

Instructor navigates to the payloadallthethings github:

### XInclude Attacks

[PayLoadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection#xinclude-attacks)

When you can't modify the **DOCTYPE** element use the **XInclude** to target

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```



Uses that payload and copies and paste it


Goes to the portswigger lab, generates traffic by clicking "view details" on one of of the items on the web application, then inside the item he clicks "view stock"

Goes back to burp suite, finds where the view stock generated traffic

finds the request in burp where the Request is POST and the url is /product/stock/

sends the request to repeater (ctrl+r)

inside the request we see this in the body

productId=2&storeId=1

we use the payload where the number two is, and we send the request to the web application and we get the content files of the /etc/passwd completing the lab.

It should be noted that the instrcuted attempted this on the number one first, it didn't work and then tried it on the number 2
so it looks like this

productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo &storeId=1

for the final payload

