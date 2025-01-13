# Lab: JWT authentication bypass via unverified signature

https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature


Lab description:
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`


Portswigger solution:
1. In the lab, log in to your own account.
    
2. In Burp, go to the **Proxy > HTTP history** tab and look at the post-login `GET /my-account` request. Observe that your session cookie is a JWT.
    
3. Double-click the payload part of the token to view its decoded JSON form in the Inspector panel. Notice that the `sub` claim contains your username. Send this request to Burp Repeater.
    
4. In Burp Repeater, change the path to `/admin` and send the request. Observe that the admin panel is only accessible when logged in as the `administrator` user.
    
5. Select the payload of the JWT again. In the Inspector panel, change the value of the `sub` claim from `wiener` to `administrator`, then click **Apply changes**.
    
6. Send the request again. Observe that you have successfully accessed the admin panel.
    
7. In the response, find the URL for deleting `carlos` (`/admin/delete?username=carlos`). Send the request to this endpoint to solve the lab.


Instructor:
In this case we are just going to get the JWT and send the without being signed

We are going to go to burp suite and go to the JWT editor extension

We are going to log into the /my-account, by using the credentials that were given to us `wiener:peter` 

Once we generate the traffic of us logging in, we can go back to the proxy->http history traffic in burp suite and see that the requests that have JWT tokens are highlighted in green.

If we investigate one of the request that is a POST method with the URL /login we can see that the session = eyJraw... is a header the rest is payload, and the finally the signature. All separate sections are seperated by a period (.)

If we follow the order of the request, once we send the POST /login we get a 302 redirect, then get a GET /my-account-?id=wiener and we can see that the request has the same cookie/JWT

In the request there are tabs we can look into and we see "Pretty" "Raw" "Hex" and "JSON Web Token"

we go to JSON Web Token and we can see more information on the JWT like the alg being RS256, we have ISS for issue by, ex for expiration date, and sub for the main claim, 

We send the Get /my-account request to repeater (ctrl + r) 

The instructor edits the request and removes the following

GET /my-account-?id=wiener to GET /my-account

There are a few things we want to test for before moving onto more complex attacks:

Adding, removing, and modifying claims, changing the signature algorithms, removing the signature entirely, and then brute forcing a weak key


So going back to our JWT tab, lets modify our claim

so lets change

sub: weiner to

sub:administrator

if we then send the request to the web application with the changed claim, we can see we logged into the administrator account!


What the instrcutor does to complete the lab is he goes on to grab the JWT after we modified it from the request which the 

Cookie: session= eyJraW...

we go to the web application (which is still logged in as weiner)

we go to the broswer console, go to the application tab, change the cookie to our modified JWT 

and we remove the weiner from 

.../my-account?id=weiner

so we change that to

/my-account

we press enter (again remember we change the cookie from the weiner cookie to the modified one we got from changing the claim in burp suite)


we then get the admin login, we go to the Admin panel once we logged into as admin, we delete carlos and we solve the lab.

Instructor says that in the JWT in repeater there is a "attack" button that we can explore attacks such as changing the "alg" to none, we can do this manually or by pressing attack and clicking on the "none" Signing Algorithm both work fine when testing but should be tested separately. 




