https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification

# Lab: JWT authentication bypass via flawed signature verification

Description:
This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. In the lab, log in to your own account.
    
2. In Burp, go to the **Proxy > HTTP history** tab and look at the post-login `GET /my-account` request. Observe that your session cookie is a JWT.
    
3. Double-click the payload part of the token to view its decoded JSON form in the **Inspector** panel. Notice that the `sub` claim contains your username. Send this request to Burp Repeater.
    
4. In Burp Repeater, change the path to `/admin` and send the request. Observe that the admin panel is only accessible when logged in as the `administrator` user.
    
5. Select the payload of the JWT again. In the **Inspector** panel, change the value of the `sub` claim to `administrator`, then click **Apply changes**.
    
6. Select the header of the JWT, then use the Inspector to change the value of the `alg` parameter to `none`. Click **Apply changes**.
    
7. In the message editor, remove the signature from the JWT, but remember to leave the trailing dot after the payload.
    
8. Send the request and observe that you have successfully accessed the admin panel.
    
9. In the response, find the URL for deleting `carlos` (`/admin/delete?username=carlos`). Send the request to this endpoint to solve the lab.


Instructor solution:

Same as the previous lab, we log into our account using the following credentials: `wiener:peter`

From there we make sure we are capturing the traffic in burp suite, we see that we get some traffic that is highlighted in green for they contain the JWT we are looking to modfiy.

we head over to `GET /my-account` request. Observe that your session cookie is a JWT.

From there we send it to Repeater, we go to the JWT tab and we start to test here.

The instructor makes it a point to test multiple things before reaching to the actual solution.

Remember to remove the /my-account?id=weiner from the request.

He attempts to do the following:

change the claim form wiener to Administrator
removing the signature from the request cookie session
and chaning the algoritm to none by clicking the Attack button, and clicking on "none"
he makes note that it gives us different types of the word "none" and this is because there might be filters in place, but he chooses the word "none"

He also states that if we change the algorithm to HA256 which fails as well (each time we test we send the request to the web application)

He changes the algorithm to none manually but didnt delete the signature at the end of the JWT because you have to do this manually to, but the attack button takes care of this for us

Anyways, changing the algorithm to none via the attack button which also removes the signature gives us access to the admin account.

Like the last lab, we copy the modify JWT/cookie, we take it to the web applicaiton, go to the browswer console, go to application, go to cookie, we paste the cookie there, we remove the ?id=weiner from the url if there is one, but there isnt because we already logged in as weiner and it just says /my-account, we add we press enter, and we get access to the administrator account, and we go to the admin panel and we delete carlos completing the lab!