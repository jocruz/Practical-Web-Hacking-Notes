Port Swigger Lab Description:
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

Port Swigger Lab Solution:
1. Browse to `/admin` and observe that you can't directly access the admin page.
2. Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
3. Change the URL in the `stockApi` parameter to `http://localhost/admin`. This should display the administration interface.
4. Read the HTML to identify the URL to delete the target user, which is:
    
    `http://localhost/admin/delete?username=carlos`
5. Submit this URL in the `stockApi` parameter, to deliver the SSRF attack.


Instructor notes:

Step through the application and functionality, the instructor states that this should always be the first thing we do when attempting to find vulnerabilities in the web application. 

Walk through the common signs, like a full URL or a partial URL in the applications. Step thorough the application functionality, the whole application so we have a good understanding of how the web application is built.

So for example, the My Account and the Authentication and Authorization, I would test separately, probably the Products page, for example I would break them down and take them on one at a time, and then in some cases, obviously, where application functionality is related, branch out and test those as well.

So we can see that this changes each time, so obviously, this just gives us a random number back, but we can check for London, Paris, and Milan for this six pack beer belt.

So lets go to Burp Suite and we want to find those requests. However we do want to note that when we do select the different stocks (its just a drop down menu and we can click on the "check stock" button to update the units that we see) we do not see a refresh of the page, we see that the units change but no page refresh.

Instructor states that this is quite common in modern SPAs or single-page applications and modern front-end frameworks using API's rather than full-on page reloads.

Going back to Burp Suite we can see the Method POST and the URL "/product/stock" we can click on any of the POST request and URLs that have that /product/stock and we investigate the Request and we can see that we get something like 

"stockApi = http%3A%2F%2Fstock.weliketoshop.net%3A8080%2F..."

We can see the stock API down here, obviously, this is encoded, but let's take a look, we'll send this to Intruder, and if we want to decode very quickly, we can obviously copy this and we can just send this to Decoder and Decode as URL.

So once we decode the URL we get the following URL:
http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1

Now if we weren't using a port swigger lab, we could do something like webhook.site and we can just use the unique URL and if we use curl and send a post request we can see on webhook.site that we do get a POST request. 

We instead go to collaborator and get started and we're going to copy our Unique URL to the the clipboard, there is a button on Collaborator that says Copy to Clipboard  and request that go through collaborator will get listed on Burp Suite Collaborator.

So we take that URL copied from Collaborator and we can head over to the POST request /product/stock and we can now enter this URL at the stockAPI parameter

we make sure to do two things that are important, we make sure add http:// before the URL that was copied, and we also make sure to highlight the new argument we placed in the parameter stockApi and we do ctrl+u to convert it to URL encoding.

We send the request and then we can go back to collaborator on burp suite and we make sure to click the button "start polling"

Doing so we will then see the requests coming in, this proofs that we can now send requests that we want from the web application on our behalf.

Okay now we want to go back to the portswigger lab description and we see want to make request to http://localhost/admin

We make sure to keep the URL encoding as is, so we should see something like

"stockAPI=http%3a//localhost/admin"

if we send this, we get a response back, (this is in Repeater by the way), we click on the Render tab and we see that we can now see an Admin panel, where we can delete the Users wiener and carlos.

Now if we inspect the page further on the tab called "Pretty" we can see that there is an <a> tag that says "/admin/delete?username=carlos"

and we just simply copy that URL and paste into the stockAPI parameter.

stockApi=http%3A//localhost/admin/delete?username=carlos


We click Send to send the request, we get a 302 Found and we can go back to the lab and we have now successfully completed the lab, however this is in the /admin part of the lab url,

the instructor also notes if we go back to Burp Suite, and send the request again and head to the Render tab we get a completed Lab in the Render.


