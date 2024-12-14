Lab Description:
This lab controls access to certain admin functionality based on the Referer header. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.

PortSwigger Solution:

1. Log in using the admin credentials.
2. Browse to the admin panel, promote `carlos`, and send the HTTP request to Burp Repeater.
3. Open a private/incognito browser window, and log in with the non-admin credentials.
4. Browse to `/admin-roles?username=carlos&action=upgrade` and observe that the request is treated as unauthorized due to the absent Referer header.
5. Copy the non-admin user's session cookie into the existing Burp Repeater request, change the username to yours, and replay it.


Instructors Lab Notes:

We start off by using the admin credentials, from here we are able to access to admin panel where we can upgrade users to admin and downgrade users to be normal users.

We upgrade Carlos and Downgrade carlos one after another. The point of this is to save the requests to Burp Suite.

From here we head back to burp suite and we investigate the request and look for these actions.

The instructor shows off the following requests with the URL named "/admin-roles?username=carlos&action=upgrade" and "/admin-roles?username=carlos&action=downgrade"

We can use either of these requests since they are performing the action we want, however obviously to save time and not to be confused we can just use the upgrade request "/admin-roles?username=carlos&action=upgrade"


So once we choose the request with the URL "/admin-roles?username=carlos&action=upgrade", we send it to Repeater (ctrl + r) and from there we start to test how the application handles this request.

The instructor goes on to remove the cookie:session from the header to see if the application will accept the request with out the cookie. The cookie is removed, and we get a response that says "Unauthorized".

The instructor says that this should be a normal response.

The instructor then goes on to log in with the other credentials that were provided by the lab "wiener:peter"

We go back to Burp and find the GET request with the URL named "/my-account?id=wiener"

From here we go ahead and get the Cookie:session from the header of the URL "/my-account?id=wiener"

we go back to repeater (back to the request where we removed carlos session cookie)

and we replace the Cookie:session that Carlos has, with the one we got from Wiener.

From here we then send the request to the application.

- This part of the video the application doesn't send back a request, however we just resend, the instructor says perhaps the request got lost somewhere, and resending it isn't a big deal.

The response we get is a HTTP/2 302 Found.

We then follow the redirect by clicking on "follow the redirect" button and the response shows us a HTTP/2 401 Unauthorized.

The instructor goes ahead and logs out and we log back in with the Administrator credentials provided by the lab.

We go to the Admin Panel, and we can see that we did in fact upgrade Carlos to Admin so our attack was successful.

- This part of the video the instructor goes on to show off a plug in for firefox that allows us to test these sort of attacks easier. It is called FireFox Multi-Account Containers
- Instructor says that we can then label the default containers to Admin1, Admin2, User1, User2
- Instructor uses two containers and these containers are noted with their distinct colors, one is blue the other is orange, it is arbitrary which color we used it doesn't matter for this lab, just matters that they are different colors
- The instructor in the blue container logs into the Administrator account and in the orange logs into the wiener account
- In the two tabs we have two different sessions, so if we wanted to use the same web applications but use it with different sessions/users we dont have to keep logging in and out, we can use the containers to accomplish this. 
- Lets go back to Burp suite
- We go to repeater and we now will change the username from carlos to wiener
- We will also change the session cookie (previously we changed it from carlos session cookie to wiener, and now we are going to replace wieners session cookie with a new session cookie from wiener since we logged into the web application again with wiener)
- To get wiener session cookie with out having to look through the Burp Suite Requests, we can just go to the console on the tab where we have wiener open and we can go to Storage, and take the cookie from the Storage (this can be found in the Storage tab and heading over to Cookies and the name is called Session and the Value is the cookie we want to use)
- We go back to Burp Suite Repeater, and plug in the new wiener cookie.
- We click send with the updated Request in Repeater
- We get a HTTP 302
- Go back to Burp Suite and we see that we completed the Lab!

