Lab Description:
This lab uses a JWT-based mechanism for handling sessions. The server supports the `jwk` parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

To solve the lab, modify and sign a JWT that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

Lab URL:
https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection

Port swigger solution:
1. In Burp, load the JWT Editor extension from the BApp store.
    
2. In the lab, log in to your own account and send the post-login `GET /my-account` request to Burp Repeater.
    
3. In Burp Repeater, change the path to `/admin` and send the request. Observe that the admin panel is only accessible when logged in as the `administrator` user.
    
4. Go to the **JWT Editor Keys** tab in Burp's main tab bar.
    
5. Click **New RSA Key**.
    
6. In the dialog, click **Generate** to automatically generate a new key pair, then click **OK** to save the key. Note that you don't need to select a key size as this will automatically be updated later.
    
7. Go back to the `GET /admin` request in Burp Repeater and switch to the extension-generated `JSON Web Token` tab.
    
8. In the payload, change the value of the `sub` claim to `administrator`.
    
9. At the bottom of the **JSON Web Token** tab, click **Attack**, then select **Embedded JWK**. When prompted, select your newly generated RSA key and click **OK**.
    
10. In the header of the JWT, observe that a `jwk` parameter has been added containing your public key.
    
11. Send the request. Observe that you have successfully accessed the admin panel.
    
12. In the response, find the URL for deleting `carlos` (`/admin/delete?username=carlos`). Send the request to this endpoint to solve the lab.

Instructor solution notes:

Same as the previous lab, we log into our account using the following credentials: `wiener:peter`

From there we make sure we are capturing the traffic in burp suite, we see that we get some traffic that is highlighted in green for they contain the JWT we are looking to modfiy.

we head over to `GET /my-account` request. Observe that your session cookie is a JWT.

From there we send it to Repeater, we go to the JWT tab and we start to test here.

Remember to remove the /my-account?id=weiner from the request in burp suite while we are in repeater (This is very important)

We go to JWT editor tab, we then go to "New RSA Key" we click generate.

From there we go back to repeater, we go to the JSON Web Token tab, in the payload we modify the Sub:Weiner to Sub:administrator

then we click on the Attack button, we click on embed JWK, we are going to see the one we generated from the JWT editor tab, we click ok and the header from our key has changed a lot, and if we investigate more we see the JWK.

we click send, to send the request and we successfully logged in as the administrator!

now we do what we did before, we copy the session cookie from the repeater after we verified it worked to log into administrator, then we go to lab web application

we open the browser console, we go to application tab, we look at the cookies, we paste our cookie in there

we make sure to change the url to /my-account and click enter

we are then logged into the administrator account on the web browser instead of burp suite, we go to the admin panel and delete carlos and successfully complete the lab




