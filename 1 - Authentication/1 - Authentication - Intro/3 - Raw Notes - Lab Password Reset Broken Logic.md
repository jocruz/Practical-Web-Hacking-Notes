Port Swigger lab:
[https://portswigger.net/web-security/authentication/other-mechanisms/lab-password-reset-broken-logic](https://portswigger.net/web-security/authentication/other-mechanisms/lab-password-reset-broken-logic)

## 🔗 Related Labs
- [[2 - Lab - Password Reset Broken Logic]]
#authentication #security #vulnerabilities #Rawnotes
# Lab: Password reset broken logic

This lab's password reset functionality is vulnerable. To solve the lab, reset Carlos's password then log in and access his "My account" page.

- Your credentials: `wiener:peter`
- Victim's username: `carlos`


Instructors notes:
Take a look around, in a real web app pen test we would look around and see what we can find, the port swigger labs shows us we can view post and leave a comment on the post we decide to view. From there we can start fuzzing them from inputs, we can search for cross site scripting, we can search for  injection commands that can be stored on the database such as SQL injection.

![[Pasted image 20241203212321.png]]

![[Pasted image 20241203212410.png]]

lets look for login requests


headers we get 
Secure; HTTPOnly; SameSite=None

![[Pasted image 20241203220636.png]]

![[Pasted image 20241203220732.png]]

![[Pasted image 20241203214724.png|1500]]



![[Pasted image 20241203220159.png|1500]]


![[Pasted image 20241203220001.png|1500]]

![[Pasted image 20241203220252.png|1500]]

![[Pasted image 20241203220337.png|1500]]


Our Notes:

Instructors notes:
Take a look around, in a real web app pen test we would look around and see what we can find, the port swigger labs shows us we can view post and leave a comment on the post we decide to view. From there we can start fuzzing them from inputs, we can search for cross site scripting, we can search for  injection commands that can be stored on the database such as SQL injection.

So it seems like we are focusing on Password reset broken logic. We start off by getting the username:password weiner:peter, from there we go to the "my account" and head over to the "Forgot My Password" link, from there we enter the email we get from the email client tab, we enter our email address, in this case lets just say weiner@pw.com for an example, from there we make sure we are capturing everything through burp suite.  

Once we submit the email through the forgot my email page, we then check out burp suite, we check out the requests and we see 2 requests that have a POST Method.
We investigate both of them, and we see the following:

we see for the first request that the username is included in the body of the post request.
We see the second Post request and we see a "Temp-forgot-password-token, we see that it has a username, we see that it has a new-password and a new-password-2, 

What we do to verfiy that this does work is we use the same username weiner and change our password to something new like password1, we go back to the login page and see if our new password did accept it. We find out that it does. We can also confirm this by seeing the success return on burp suite that says HTTP/2 302 Found.

Once we confirm that it does work we confirmed a couple of things. One that token "temp-forgot-password-token" isn't linked to a specific account and can be reused. If we tamper with the token, we find out that it can also be tampered with, change it to something like "tampered" on the request both on line 1 and line 23 (where ever the token appears) we the confirm that the token isn't bound to anything its reusable and can be tampared. 

next we confirm also that we can change the passwords and it gets accepted. This means that as long as the tokens match up wherever they appear in the request we are good to go to change any username and password

We change the username to the victims username which is 'carlos' and change the password to whatever we want, I left it as "password" so now the new-password-1 and new-password-2 are equal to "password" and we send the request once again getting the response "HTTP/2 302 Found"

We go back to the login page of the portswigger lab and log in with our victims username and their new credentials and we successfully login. 
We have successfully exploited the Password reset broken logic that is in the web application.


PortSwigger solution:
1. With Burp running, click the **Forgot your password?** link and enter your own username.
2. Click the **Email client** button to view the password reset email that was sent. Click the link in the email and reset your password to whatever you want.
3. In Burp, go to **Proxy > HTTP history** and study the requests and responses for the password reset functionality. Observe that the reset token is provided as a URL query parameter in the reset email. Notice that when you submit your new password, the `POST /forgot-password?temp-forgot-password-token` request contains the username as hidden input. Send this request to Burp Repeater.
4. In Burp Repeater, observe that the password reset functionality still works even if you delete the value of the `temp-forgot-password-token` parameter in both the URL and request body. This confirms that the token is not being checked when you submit the new password.
5. In the browser, request a new password reset and change your password again. Send the `POST /forgot-password?temp-forgot-password-token` request to Burp Repeater again.
6. In Burp Repeater, delete the value of the `temp-forgot-password-token` parameter in both the URL and request body. Change the `username` parameter to `carlos`. Set the new password to whatever you want and send the request.
7. In the browser, log in to Carlos's account using the new password you just set. Click **My account** to solve the lab.




