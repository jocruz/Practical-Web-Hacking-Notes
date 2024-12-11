
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

We see the request in Repeater we want to test for Access control vulnerabiliies and since the request does in fact has /my-account?id=wiener in the request, we can change the wiener username and see if we can change that to our victims username (or any username really since we are trying to prove that there are Access Control vulnerability)

and once we change it to carlos we see on the response render that we can now see that we have access to Carlos page and Carlos API key. We copy and API key and submit it to Portswigger completing the lab.

Some things to take away is that the Cookie session should be tied to the user Wiener and not be allowed to be used by username Carlos.

In my PWPA certification exam I actually found this exact issue and here is my technical finding:

"**Discovered a Broken Function Level Authorization (BFLA) vulnerability. Using two user accounts, John and Test, we tested the DELETE functionality on the /vault/delete endpoint.  
  
First, we deleted a vault entry using John's account with the session cookie: connect.sid=s%3AcFTQPiBeRrDhNd9aZ512xr5MD-CUFFSs.obzrb7yWit2WnNUd7pCApEyz3S0CKd9k88knWouRNa8.  
  
Then, we logged into the Test account and captured its session cookie: connect.sid=s%3Ag8EdtyM8PDmFEAR_cZr9UcVK8tfX5bxy.Xg4AI7ZIn0nROTsaU8FbtqmBv1CfufMJwBCrqFl%2Ff2c.  
  
By replacing John's session cookie with Test's cookie in the DELETE request, we could still delete the vault entry. The server responded with HTTP/1.1 200 OK and Item deleted successfully, showing that the deletion was improperly authorized. The Autorize extension also flagged this as a bypass. This vulnerability allows lower-privileged users to perform actions that should be restricted to higher-privileged users.**"



#Rawnotes #raw-notes 