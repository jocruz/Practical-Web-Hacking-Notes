# Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

Lab URL: 

https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink

PortSwigger descirption

This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search`, which you can control using the website URL.

Portswigger solution
To solve this lab, perform a cross-site scripting attack that calls the `alert` function.

1. Enter a random alphanumeric string into the search box.
2. Right-click and inspect the element, and observe that your random string has been placed inside an `img src` attribute.
3. Break out of the `img` attribute by searching for:
    
    `"><svg onload=alert(1)>`


Instructor Solution:

Dom Invader:

Burp Suite community edition:
Proxy -> Intercept -> Browser in top right corner
This will open up a browser, and we can navigate to the lab url 

and from here we can do Ctrl +Shift + c and this will open the console, and we can click on Dom Invader, copy and paste the canary value into the search bar on the lab web application, it will then tell us if we can exploit it

Screenshot of it finding a sink and showing green exploit button:

![[Pasted image 20250108001330.png]]

So we can also click on the stack trace hyperlink which shows us where our canary is, we can see where in the source code the canary was being used and we find 

document.write('img src="/resources/images/tracker.gif?searchTerms+query+'">');

so if we click on exploit, and review our value, our payload lands inside 

```
<img src="/resources/images/tracker.gif?searchTerms=tpem9by9">
```

From here we just have to write a payload to escape this.

But looking at the stack trace we know that our payload gets passed through javascript, however the document.write is the final step to where it really lands which is inside the page and the html attribute.

We aren't in the href, so we have to do a different payload which is 

```
"><img src=x onerror=prompt()'
```

and we complete the lab