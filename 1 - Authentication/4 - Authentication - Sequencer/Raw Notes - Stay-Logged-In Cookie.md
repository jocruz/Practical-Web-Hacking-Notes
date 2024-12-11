
# Lab: Brute-forcing a stay-logged-in cookie

Sometimes we want to understand if a token just appears to be random and we can use burps tool sequencer to achieve this.

When would we do this?
If an app looks like it's running its own session management

If the session token is short, repetitive, or doesn't appear to be random


Lab: Brute-forcing a stay-logged-in cookie

This lab allows users to stay logged in even after they close their browser session. The cookie used to provide this functionality is vulnerable to brute-forcing.

To solve the lab, brute-force Carlos's cookie to gain access to his **My account** page.

- Your credentials: `wiener:peter`
- Victim's username: `carlos`
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

We head over to My account, and we log in with the credentials that the lab provides us


We take note that there is a Stay logged in check box, for this lab we will check the box to stay logged in, however in a real world application we would want to run a test one with not stayed logged in, and one with it checked to see the differences in the request.

Once we log in, we head over to burp suite, go to the proxy tab, check the HTTP history, then check the /login request

And then from there we check out the request, and we can see something in the request which is
```html
username=wiener&password=peter&stay-logged-in=on
```

On the response we see that on line 3 it says the following 

```html
Set-Cookie: stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw
```


and we also have the set cookie session

```html
Set-Cookie: session=urieAQvk9gEnrKajx77MEzOZtORFTlQs;
```

So lets send this to repeater and lets see what we can do with the cookies

So we are now in the Repeater tab and we are sending the request to the web application by hitting the send button. And what we observe is the following
That the Set-Cookie:stay-logged-in is static, however the Set-Cookie: session is changing.

This is what we are looking for!

This indicates that the static cookie (stay-logged-in) perhaps never expires, perhaps it's weak, perhaps we can generate it ourselves, the fact that it says the same it is suspicious. It is also very important to note whether or not the cookie is using a preset of characters, perhaps its appending the same characters something to look out for.

Okay next we right click on the request and we send it over to Sequencer.

What we want to do, is in the Sequencer tab, we can see we have our request as #1, and we also have an option called "Token location within response" which is set for us.

We are going to click on the custom location and configure it, so we click on Custom location, 

We get another pop up menu that says Define custom token location and from here we can just simply highlight the token that is constantly changing. Highlighting it will auto complete the fields we need. The instructor tells us the expectation is just to observe what a "normal cookie" looks like.

We do start live capture, and then the Live capture begins, it's going to capture dozens of tokens, during the capture, we can click "Analyze now" and the report shows that the "Overall result:
The overall quality of randomness within the sample is estimated to be :execllent. At a significance level of 1%, the amount of effective entropy is estimated to be: 149bits."

The instructor says we can continue to capture, however we can clearly see that the overall report shows that it is fine, and the significance levels is in good position, its all green no red which would signify something wrong.

Ok so now we know what a normal cookie that seems secure might look like. A good standing on the Overall Analysis and a good standing on the Character level analysis summary tab

So lets check out the other cookie, the one that does NOT change and is static.

The instructor suggest before we bring it to sequencer, we can actually take a quick note of the cookie that it looks like it might be Base64 and there is a decoder feature on Burp Suite. So we can just copy and paste the cookie onto the Decoder tab input field box, and we can just copy and paste it and "decode as ..." and we see decode as base 64, we click that and we there and we see something interesting.


that this cookie
Set-Cookie: stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw

actually decodes to

wiener:51dc30ddc473d43a6011e9ebba6ca770

so its the username + an md5 hash!
So we can go to cyberchef.io and we can double check here, and we can confirm our findings as well.

Burp suite also has an extension called Hackvertor and we we can go to Hackvertor and copy and paste our cookie there 


we get the following
<@d_base64>d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw<@/d_base64>



wiener:51dc30ddc473d43a6011e9ebba6ca770

Hackvertor screenshot:
![[Pasted image 20241206203932.png]]


We noticed it puts tags, which means if we deal with something more complicated we can nest tags and start either decoding more complicated structures or use this to create payloads to bypass firewalls

OK lets go back to Sequencer, and lets go to the Sequencer settings and lets head over to the section called "Token Handling" and we can check off Base64-decode before analyzing button

Sequencer Setting screenshot:

![[Pasted image 20241206204225.png]]

This is because if it analysis the base 64 ENCODED PAYLOAD  IT IS GOING TO GET A DIFFERENT RESULT to analyzing the decoded token

Starting the live capture again, we can click on analyze now, we can see that the report shows "Extremely poor" and if we go to the character-level analysis, we can see that it is a static token, with all red and under .0001 percent.


Screenshot of Character Analysis:
![[Pasted image 20241206205527.png]]



Okay so now we head back to our repeater, and we want to experiment, what happens if we remove the non static cookie? We remove the non static cookie the session cookie, and we send the request, we are still able to get a response back and we are still logged in, so this is a quirk since we are still logged in

so lets open up the terminal and lets verify what this md5 hash might be, so lets go to 

```bash
echo -n 'peter' | md5sum 
```

and we do get confirmation that it is just the username+md5hash of the password

Remember our given credentials are username:password which is `wiener:peter`
Screenshot of the kali linux command 



![[Pasted image 20241206205830.png]]

So how do we set this up in intruder, 

So lets go ahead and see our Repeater tab. We are able to delete the static cookie and remained logged in, which is confirmed by the Response Render, and now we look to simplify our headers.
The current header shows GET /my-account?id=wiener HTTP/2

The instructor says we should try to see if we can remove the id=wiener and see if we get a different response or if we stay logged in

After removing it, we can see we still stay logged in.

Removing this actually help us because this is something less we have to deal with when it comes to brute forcing and something less we would have to take into account when going in for an attack it isn't just for simplifing the request.

Ok once we have simplified the request, we can now take the simplified request to Intruder.


Now we place a positional marker on the Cookie: stay-logged-in= "..."

and we are going to go to payloads, we are going to add in our payload of passwords which is provided by the lab, we have this saved as passwords.txt.

Okay now that we set up the payload we go ahead and go to the second set of settings called Payload Processing. Here click Add and we select the payload processing rule called Hash, and for the second rule we add MD5. 



We click OK and the next thing is we want to add in a prefix, which is the same process of clicking Add, we click "Add Prefix", we add in our Victims name carlos with a colon so "carlos:"
and the last rule is Encode and Base64. 

Screenshot of Payload processing Rules all added:

![[Pasted image 20241206212328.png]]

We click start attack, (It is a Sniper attack)

And we can see that in our results we see two things. The status Code is 200 and the Length is 3346. 

We can grab the payload that meets both of these criterias and we copy that payload and send it to repeater.

So we grab the payload, we go back to repeater with our simplified request with no id in the header and no static cookie, and we replace the current stay-logged-in cookie with the payload we got, and we can send the request and in the Response render we can see that we are now logged in as Carlos Completing the lab!.

#Rawnotes #raw-notes 