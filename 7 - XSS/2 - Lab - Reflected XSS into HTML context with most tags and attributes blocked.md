# Lab: Reflected XSS into HTML context with most tags and attributes blocked
Lab URL:
https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-most-tags-and-attributes-blocked


This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.

To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the `print()` function.

PortSwigger solution:
1. Inject a standard XSS vector, such as:
    
    `<img src=1 onerror=print()>`
2. Observe that this gets blocked. In the next few steps, we'll use use Burp Intruder to test which tags and attributes are being blocked.
3. Open Burp's browser and use the search function in the lab. Send the resulting request to Burp Intruder.
4. In Burp Intruder, replace the value of the search term with: `<>`
5. Place the cursor between the angle brackets and click **Add §** to create a payload position. The value of the search term should now look like: `<§§>`
6. Visit the [XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) and click **Copy tags to clipboard**.
7. In the **Payloads** side panel, under **Payload configuration**, click **Paste** to paste the list of tags into the payloads list. Click  **Start attack**.
8. When the attack is finished, review the results. Note that most payloads caused a `400` response, but the `body` payload caused a `200` response.
9. Go back to Burp Intruder and replace your search term with:
    
    `<body%20=1>`
10. Place the cursor before the `=` character and click **Add §** to create a payload position. The value of the search term should now look like: `<body%20§§=1>`
11. Visit the [XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) and click **Copy events to clipboard**.
12. In the **Payloads** side panel, under **Payload configuration**, click **Clear** to remove the previous payloads. Then click **Paste** to paste the list of attributes into the payloads list. Click  **Start attack**.
13. When the attack is finished, review the results. Note that most payloads caused a `400` response, but the `onresize` payload caused a `200` response.
14. Go to the exploit server and paste the following code, replacing `YOUR-LAB-ID` with your lab ID:
    
    `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>`
15. Click **Store** and **Deliver exploit to victim**.


# Cross-site scripting (XSS) cheat sheet

This cross-site scripting (XSS) cheat sheet contains many vectors that can help you bypass WAFs and filters. You can select vectors by the event, tag or browser and a proof of concept is included for every vector.

https://portswigger.net/web-security/cross-site-scripting/cheat-sheet


Instructor solution:

Accessing the lab we see that the lab presents us a search bar, we type in "qwerty" and we are able to see the results of what we typed meaning it is reflected back at us

Screenshot of 'qwerty' being reflected.

![[Pasted image 20250107175005.png]]

Instructor tells us to press "ctrl + u" and we are going to search 'qwerty' and he says "we can see there is one instance of it, and it is in-between two h1 tags."

Screenshot of source code where 'qwerty' appears in-between two tags:
![[Pasted image 20250107175621.png]]


So the goal will be replacing qwerty with javascript, so if we type in the 
```html
<script>prompt()</script>
```

if we submit the javacript and click "search", the web application returns "Tag is not allowed"

This is a good hint, if this tag "script" isn't allowed, then which are? 

The instructor goes on to just search

```html
<script>
```

and the web application returns the same "Tag is not allowed"

The instructor attempts another payload

```html
<img src=x onerror=promp()>
```

We get the same message "Tag is not allowed", we once again remove everything but 

```html
<img>
```

to check once again if its only checking the first part of the tag. We get the same error if we just submit the img tag alone.

We need to check if the filter is being applied correctly, so we need to get a payload that holds different tags to make sure that each tag is actually getting filtered. 

So from here we go to https://portswigger.net/web-security/cross-site-scripting/cheat-sheet and we grab the entire list of html tags and Event handlers tags that we can use, 

So we go to burp suite, we find where our GET request is, we check the url and see if we can find our previous attempts/payloads the URL looks like /?search=%3Cimg

We send it to repeater (ctrl + r)

We add a positional marker around the word img 

we want to make sure that its simply just the string img that we put a positional mark and not add anything else, because in future web pen test, the less we have in our payload the better because we have 

```html
<img onerror=prompt()>
```
we won't know if its the onerror attribute, the prompt the parenthesis, or anything else we have to keep it simple.

Instructor makes note that the payload encoding is turn on so it will URL encode the characters ```

```.
/\=<>?+&*;:"{}|^`#
```

So we import the tags from the portswigger cheat sheet and we put them in our payload, and we run the attack. 

We will organize it by status code and we will find that there are some status codes with 200 with the corresponding payloads.

We see that body is one of the accepted payloads returning 200, so we go back to the web application and search with

```html
<body onerror=prompt()>
```

and the application will return "Attribute is not allowed" and this is referring to the onerror

so we return to repeater, now that we know body is accepted we can put a positional marker around onerror and instead of the tags as the payload now we use the event handler from the portswigger cheatsheet. 

We run the attack and now we see that onbeforeinput and onbeforetoggle works as they return a 200 status code.

