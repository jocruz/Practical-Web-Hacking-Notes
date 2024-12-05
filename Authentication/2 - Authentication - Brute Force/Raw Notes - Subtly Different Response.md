## Challenge Walkthrough
# Lab: Username enumeration via subtly different responses
https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses


Similar to the brute force lab what we want to do is look at the POST /login and we see that our body has the username=test&password=test data. From here 

Go to Settings, go to Grep - Match option and we can match all of the results for key words

So what we want to do is go to any results on the results tab and grab "Invalid username or password." and we add it. Once we add it (it will warn us that it is a live attack however we can do that as its just a portswigger lab and not a real attack) and once we add it we will get a lot of columns added to the Results tab however in the same settings of Grep - Match we can just remove columns from the list, so we can remove everything except the "invalid username or password" (remember this was taken from the request so its best practice to copy and paste the string or results from the page that we want to filter out) so now that we have our added column we see that almost all of the request have that string "Invalid username or password" with the exception of 1 which in this case is accounts (or what ever the answer is depending on the lab). So we have successfully got the username.

Now we rinse and repeat for the password we place a position marker on test on the password field and we can see that all the character length are above 3300+ with the exception of one of the password which is 1qaz2wsx (or whatever the lab answer is, I say this because it differs when you load up the lab)

Ok , so the lab is called enumeration via subtly different responses and this lab was mainly to focus on using burp suit and the intruder tool to verify the responses specifically the length, and we learn that we can utilize the Grep - Match settings in a live attack. 

The other quick way of doing this was to use ffuf however it was important to learn to focus on utilizing burp suite community edition. 

#raw-notes 

