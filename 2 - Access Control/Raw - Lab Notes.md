
Burp Suite Solution

1. Log in using the supplied credentials and go to your account page.
3. Note that the URL contains your username in the "id" parameter.
4. Send the request to Burp Repeater.
5. Change the "id" parameter to `carlos`.
6. Retrieve and submit the API key for `carlos`.



Instructor:
You can log in to your own account using the following credentials: `wiener:peter`

When we log into the account we get 
"your username is :wiener"
"Your API Key is: iNmKbmCgvGSoRt...."

Taking note of the URL, it has this in the URL: /my-account?id=wiener

The Instructor Logs out, and we go to Burp Suite and we head over to Proxy and HTTP History, 
If we go to the POST request with the URL called /login we see investigate the Request, we see the following


If we see the GET Request with the URL method we see /my-account?id=wiener and we send it to repeater (ctrl+r)

We see the request in Repeater 