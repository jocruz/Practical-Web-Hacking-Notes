# Lab: Multi-step process with no access control on one step

PRACTITIONER

This lab has an admin panel with a flawed multi-step process for changing a user's role. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.


1. Log in using the admin credentials.
2. Browse to the admin panel, promote `carlos`, and send the confirmation HTTP request to Burp Repeater.
3. Open a private/incognito browser window, and log in with the non-admin credentials.
4. Copy the non-admin user's session cookie into the existing Repeater request, change the username to yours, and replay it.

Instructor notes:

We access the lab and we want to focus on the Administrator Login. We login with the credentials, and after that we go to the Admin panel as one of the hyperlinks presented to us.

Here we can see that we have the power to upgrade user to Admin and to Downgrade a user to non-admin.

We upgrade a user to Admin in this case Carlos with the idea to be able to have the request be reached by burp suite.

We then downgrade the user Carlos back to a Normal user.

Again this is just to keep track of the Requests.

The instructor then has us log out, then log back in with the account Wiener. 

We log in with Wiener.

We go take a look at the POST request in Burp suite.

If we take a look at the POST request with the URL named /admin-roles then we are able to see that we can see in the body of the request that it says 
"action=upgrade&confirmed=true&username=carlos"

In the response we see that we have a HTTP/2 302 Found. Which is a redirect. 

What we need to send this request to Repeater.

We then go to the Request with the GET method and the URL named "/my-account?id=wiener" and what we do is grab the session cookie from Wiener.

We head to repeater (remember we sent the request from /admin-roles to repeater)

OK from here we use Wieners cookie and paste it in to replace the session cookie that we have in repeater. 

We then send the request and we get a successful HTTP/2 302.

The instructor tells us this warning:

"You have to be a little bit careful if I just sent this right away this might have updated the user as an admin and then once we copy and paste the session token then we send the same thing and if we dont see a difference at the results and then see carlos has been updated to Admin then we are seeing a false positive so we have to make sure we take it step by step to not mess anything up"


Okay once we got the 302, we click the button "Follow the redirection" then we see we get a 401 unauthorized. But this doesn't tell us that the request was actually processed or not, we just know it forwarded us to a place that we are not allow to go to.

If we log back into Administrator, we can see that Carlos is an Admin.

Now we want to repeat the same for the user Wiener. 

We repeat the process of logging out, we log back into Weiner to get their session cookie

We go back to the GET Request of /my-account?id=wiener, get the session cookie, go back to Repeater, then change the username from carlos to weiner (remember that what we are changing is from this string in the body of the POST request of /admin-roles action=upgrade&confirmed=true&username=carlos, we change this from carlos to wiener)

We send the request, we get a 302, and then we head back to the lab to see that we have successfully completed the lab because we updated all the users to Admin, and specfically Wiener. 
"To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator." this is what we accomplished



![[Pasted image 20241211131540.png]]
#raw #accesscontrol #cookie 