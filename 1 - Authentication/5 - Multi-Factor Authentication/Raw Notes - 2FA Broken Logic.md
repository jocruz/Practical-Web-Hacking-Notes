
# Lab: 2FA broken logic

PRACTITIONER

This lab's two-factor authentication is vulnerable due to its flawed logic. To solve the lab, access Carlos's account page.

- Your credentials: `wiener:peter`
- Victim's username: `carlos`

You also have access to the email server to receive your 2FA verification code.

PortSwigger solution:
1. With Burp running, log in to your own account and investigate the 2FA verification process. Notice that in the `POST /login2` request, the `verify` parameter is used to determine which user's account is being accessed.
2. Log out of your account.
3. Send the `GET /login2` request to Burp Repeater. Change the value of the `verify` parameter to `carlos` and send the request. This ensures that a temporary 2FA code is generated for Carlos.
4. Go to the login page and enter your username and password. Then, submit an invalid 2FA code.
5. Send the `POST /login2` request to Burp Intruder.
6. In Burp Intruder, set the `verify` parameter to `carlos` and add a payload position to the `mfa-code` parameter. Brute-force the verification code.
7. Load the 302 response in the browser.
8. Click **My account** to solve the lab.
   
Instructor solution:

Steps:
We login with the users credentials and we try to find the requests on the Burp Suite.
We go through each POST request and we use the highlighting feature and we highlight it with a color.

We look at the URL named /login and /login2

And we inspect the Requests. We are able to identify 2 things here, one that there is something called

Cookie: verify=wiener; session=rQJbtZy0DedQsXg0psIKs88QIMboLft 

we see that there is this verify and session, and the verify has the username from the credentials that we used. 

Of we go to /login2 then we see that at the bottom request there is the mfa-code=0962
we make sure to highlight this /login2 too

The instructor goes to the GET request that has the URL labled as  /login2 to repeater (ctrl+r)

And we change the verify parameter from wiener to carlos

and we send the GET request, what we are doing here is getting the MFA code for the user carlos

So now we go look for the POST request with the URL named /login2 and the instructor says he wants to brute force the MFA token that was generated for Carlos. We send the request to Intruder and we will begin an attack here.

We make sure to change the verify parameter to carlos and then we will place a position marker to 0962

And we will go to the payloads tab and we will switch the payload type from Simple List to Brute Forcer.

From there we change the character set to 0123456789
We make sure the min length and max length is set to 4 which should represent the min and max length of the MFA code we saw in our requests (0962).

We begin to brute force this and we see that the payload is returns with a status code of 302 (all the other ones are 200 status code) we see that we get a payload of 0262

If we switch tabs from Request to Response in the results, we see that we get a session cookie

We copy and paste that cookie and we go back to the browser, we open up the console on the browser, 

And from there, we go to the Application tab in the console, and we see that there is a there is already a verify wiener, we can delete that stored cookie in there and place in our own, there is a Session with a value of a cookie, and we can copy and paste our cookie value from the attack.

and we head to the url and add in /my-account?id=carlos and we complete the lab!

#Rawnotes #raw-notes 