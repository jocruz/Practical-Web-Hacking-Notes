Portswigger Lab: https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system
# Lab: Basic SSRF against another back-end system

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal `192.168.0.X` range for an admin interface on port `8080`, then use it to delete the user `carlos`.

Portswigger Solution:

1. Visit a product, click **Check stock**, intercept the request in Burp Suite, and send it to Burp Intruder.
2. Change the `stockApi` parameter to `http://192.168.0.1:8080/admin` then highlight the final octet of the IP address (the number `1`) and click **Add §**.
3. In the **Payloads** side panel, change the payload type to **Numbers**, and enter 1, 255, and 1 in the **From** and **To** and **Step** boxes respectively.
4. Click  **Start attack**.
5. Click on the **Status** column to sort it by status code ascending. You should see a single entry with a status of `200`, showing an admin interface.
6. Click on this request, send it to Burp Repeater, and change the path in the `stockApi` to: `/admin/delete?username=carlos`

Instructor Lab Notes:

Similar to the other notes, we want to to explore the web application but we can just quickly go to the "view details" button to investigate further

We then just check the stock and change the drop down menu to generate request to send over to Burp Suite.

We then check out the Burp Suite requests and we can investigate the POST requests with the URL of "/product/stock"

we send it over to repeater (ctrl+r)

we then highlight the argument of stockAPI and we do ctrl+shift+u to decode the URL and we get stockApi=http://192.168.0.1:8080/product/stock/check?productId=1&storeId=1

We know we are looking for an admin panel on port 8080.

So what we do is we change the url to http://192.168.0.1:8080/admin when we successfully send it to Intruder.

We then go to Payloads and we are going to start an attack by changing the payload to Payload Type to Numbers.

We make it go from 1 to 255.

We start the attack and we notice that since we are on the Burp Community we are throttled and it goes super slow.

We are now going to go to an extension called Turbo Intruder. We install this extension and once we install it we can return to our Repeater tab and then we can right click and see we have an Extensions option. We click on Extensions and we can access Turbo Intruder.

The nice thing about Turbo Intruder, a lot of is easy to navigate.

We are not going to touch any of the code so instead we will target the for loop in the python code

so right now the code we see is the following:

```python
for word in open ('/usr/share/dict/words'):
	engine.queue(target.req,word.rstrip())
```

We will delete this for loop and we will write our own for loop to be able to complete the lab

we change it to the following:

```python
for i in range(1,256):
	stockApi = 'http://192.168.0.$s:8080/admin' % i
	engine.queue(target.req, stockApi)
```

the last thing we change is at the top of this extension we see the request and we have to change the argument to %s

so we change the request again in the extension to 
stockApi=%s

We are going to loop to 1 to 255 and it will generate the numbers in the loop and replace the $s in the url and we are going to send the request to the stockApi to where we have the %s

We send the attack 

![[Pasted image 20241214020309.png]]

We then Start the Attack.

So we filter the results by the status. We see that one status is 200, and we can also note that the Queue ID is the number we are looking for which is 236.

From here we look at that request/payload/Row in Turbo Intruder and we can just grab the argument that was generated as our payload which is http://192.168.0.236:8080/admin

If we Render the request in Turbo Intruder we can see we do get the admin panel.

So we can actually just scroll down the Pretty tab, still in Intruder Turbo, and we just see if we can find the link that we need which we do find it in the a tag which is 

<a href='http://192.168.0.236:8080/admin/delete?username=carlos'>  

We grab that link, go back to Repeater, we post http://192.168.0.236:8080/admin/delete?username=carlos and we just change the stockapi=http://192.168.0.236:8080/admin/delete?username=carlos and we send it, and we just Render it and we get a completed lab.