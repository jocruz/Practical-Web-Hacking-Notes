#brute-force, #authentication, #raw-notes.

Brute Force Attacks:

Using Trail & Error to guess credentials

Target usernames, passwords, email addresses, etc

Use information we've found or from lists and public sources

Watch for changes in:
Status codes
Errors messages
Content Length
Response Times

ALWAYS WATCH THE CONTENT LENGTH - always pay attention to this

Use wordlists to save us time, over time merge them but for our purposes these are ideal
Assetnote
Seclists
Custom

We can easily find lists by using find command and the -iname flag
find /usr/share/seclists -iname "\*subdomain\*"

find /usr/share/seclists -iname "\*username\*"


[https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses)


Lab steps:
go to my account

We see a login, and we try to login, for the first login we just want to enter wrong credentials to see what the message we get shown. We see that we get an "Invalid Username" message.

The instructor says "notice how username and password are both 8 characters, same string length so if you are just looking at the content length, in intruder you might not see any difference, we can use the filter or search to find another things. We can also look at timing differences and other things when the error message isn't so verbose or precise"


So lets head to burp suite, and lets look at the proxy/HTTP History

And from here lets looks at the POST request that has the URL /login. From here we take a look at the body and see that it has username=test&password=test and lets send this to intruder.

lets modify the request by placing a **Payload Position Marker** on the string "test",  the instructor says to focus on the username first, but I think he is doing this because the message we got from the invalid login told us we have an invalid username so we are focusing on that first.

We go to payload tab, and we add in a our username and password list that Portswigger gives us from the lab url. When we start the attack we notice that there is a high character length. 

If we look at the response, so once we find the payload within the wordlist that we use that does show the character length that is high (in the video its 3250) then we look at the request, we examine it and then move over to the tab that shows Render. We look at the rendered page and see what message we get. This time we get an "Incorrect Password". So what does this mean? This means we confirmed that we have used a validated username, and we now know that the invalid message does in fact correlate to the username or password. Since we don't have burp suite pro we can click on the filter tab and we can use a keyword like "invalid username" and do a Negative search so any result that doesn't have "invalid username" would show making the process faster.

From the results from the payload, we just click on the request with the high character length and just press ctrl+i to send it to intruder, and we now have the username from the wordlist that has a payload position marker.

Now we do the same process with the string password=test, we put a payload position marker around "test" in the password field and we add in the payload list from the portswigger download and we see the results (the results are throttled due to the community edition) from here we see that only one password has a column Length of 187 (could differ depending on the lab we should just note that it is really small compared to the other ones that are 3250) so we have michelle as a password.

So now in the instructor video we were able to brute force the username and password of adkit and michelle as username and password.

Next up we are going to solve this lab using FUZZ. 

OK so we grab the post request and we save it in a file. We go inside of the request and replace the values 

username=FUZZUSER&password=FUZZPASS

so we save it and head over to our terminal.

We do ffuf -request req.txt -request-proto https -mode clusterbomb -w /home/kali/usernames.txt:FUZZUSER -w /home/kali/passwords.txt:FUZZPASS 

since we are looking for a 302 we can just search for 302 so we add in the -mc flag

We do ffuf -request req.txt -request-proto https -mode clusterbomb -w /home/kali/usernames.txt:FUZZUSER -w /home/kali/passwords.txt:FUZZPASS -mc 302

and we get back the results

FUZZPASS: michelle
FUZZUSER: adkit


now if we didn't do/add the -mc flag, we would get a lot of results back and we would have to just see what is getting repeated in the results and we can start filtering things out such as filtering out the Size and the Words. 

#Rawnotes #raw-notes 