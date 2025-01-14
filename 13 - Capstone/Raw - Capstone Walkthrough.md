
Instructor begins by opening up the capstone and going through the web application

since its a local host, we begin at the root of the web applciation in this case its just local host but we can think of this as the beggining of the web application

He pressed ctrl+ u to view the source code, looks to see if there is anything funny going on at the root of the web application.

we find a `<script src="/js/custom.js`

we click on it to see where it leads us however it leads us to "CANNOT GET /js/custom.js" message so we move on

We head to the Attraction tab of the Nav bar

we land in localhost/login?error=You%20need%20to%20login%20before%20accessing%20this%20page

The instructor tells us to take note of the error, and this could be a point of XSS injection/Template Injection, he goes on to replace the error message with a random string of text to see what happens and we see it gets reflected to the web broswer

localhost/login?error=Testing

The error message then reflects our payload string so we should take note of this

We click on Register/Login tab in the Nav bar

The instructor says we should always make 2 accounts to be able to test

Account A and Account B

he makes two accounts Poppy and Eflie

Creds:pass
poppy:poppy
poppy@test.com

elfie:elfie
elfie@test.com

once we register both users, we get two verification emails, and the instructor suggest before we verify, we should take things slow and verify one and step through the application with poppy first. This is so that we can start checking for IDOR or BLAF/BLAC with the unverified user using the verified users web traffic

We look inside the email and we find `http://localhost/verify/2`

The instructor says that we can now see that the verification url is very insecure and the id at the end is guessable meaning we can bruteforce this or we can just verify any user as long as we keep guessing. What comes to mine is just being able to attack this with a sniper attack on burp suite and just using a payload of 1-100 to see if we can get verified or using a python script to generate the payload and attempt to hit this URL with a POST request using Curl.

We verify Poppy account and we get a localhost/login?success=Account%20Verified.%20You%20Can%20Now%20Login

So now we just login into the poppy account post verification.

Our JWT editor burp suite extension tools, if we look at our traffic it should show us where the JWT tokens are located in our web app.

instructor tells us we should be keeping note that we will have to test the JWT

he goes into /attractions, then looks at the source code, and we see nothing exciting or of interest.

We have /Shop and /Basket so we should focus on /Shop to be able to fill up our basket.

We add in a JimaJura baseball cap.

In /Shop you inspects the web app and inspects the button "Add to Basket" to be able to see how that is being handled.

In our traffic since we added the baseball cap we see that we have a /shop/basket/add POST request so that is interesting

in that request we see

itemid=1&quantity=1

Going to /Basket in the web app, we click on checkout, we land in localhost/checkout and we fill out the shipping address and payment detail forms.

We click on Pay now and we get a payment successful message once again taking note that the URL parameter is being reflected on the webpage

localhost?/success=Payment%20successful


So this is an indicator that we should be testing for payment injection and payment race conditions


The instructor logs out of Poppy and logs into Elfie

and goes through the entire web application and checkouts an item and pays for it and was successful and we verified that the verification is actually useless

So at this point we understand the entire web application and its end points 

Instructor tells us we should be taking note of everything and its end points as well as its burp suite history 

Then says that we should take note that when we go to /basket it also made a call to /purchase-history?id=2

and tells us we should be testing the purchase history end point with IDOR and Injection

He begins testing the IDOR 

so we head to /purchase-history?id=2 in burp suite and we send a baseline request to the web application using repeater and we modify the GET request in the headers in repeater

GET /purchase-history?id=2 and we change it to /purchase-history?id=1

however we get back very similar json object back, so what we want to do is add way more unique items to our basket and check them out via Elfies account.

Once we purchased different unique items. We log out of Elfie and log back into the Poppy account

We then go into the /Basket endpoint and we then go back to burp suite find the /purchase-history?id=1 for the Poppy account, we then attempt the IDOR attack by changing the 
GET /purchase-history?id=1 to GET /purchase-history?id=2

we can actually confirm the user by just checking the JWT tab in repeater.

and once we send the request with the modify id we then get a successful Broken Access Control (BOLA/IDOR) attack because we get back different users purchase history!

In the same request he goes on to remove the Cookie: token =eyJhb... from the request and sends it to the web app in repeater and we still get data back so now we know the JWT isnt even used in that end point!

He makes it a point that before we even use the Json web token tab to start attacking the jwt with the tool we have to verify that the JWT is even needed in the headers because if it isnt and we are able to do attacks such as setting the algorithm to none then we didnt verify an attack because the JWT isnt even used. He stresses how important this is.

He goes onto the /basket endpoint and we see that if we remove the Cookie: token header from there and send it to the application we get a 302 not found returned from the application

We confirmed that the /basket because when we werent logged in we click on the /basket we got forwarded to login as a user first.

He goes on to say since the JWT is being used here we can try to crack the JWT using the jwt tool on kali

python3 jwt_tool.py [cookie] --crack -d /usr/share/seclists/master-seclists/Passwords/xato-net-10-million-passwords-10000.txt

we crack the JWT and we get "secret is the CORRECT key"

we go to jwt.io

we change the id from 1 to 2 (poppy to elfie) and we modify the algo to string "secret"

and from there we now crafted a jwt that is elfies and we return to burp suite, we replace the current cookie that is poppy's and we replace it with the new crafted one from jwt.io and in repeater after replacing it we send it to the web application and we get 304 not modified and this confirms we can forge tokens and it works. If we use an incorrect token we get 302 found and we got redirected.


Moving on we can now go back to jwt.io and change the id to an id that we know that does not exist, so poppy is 1, elfie is 2, and 3 should be nobody. So lets check what happens if we switch it to 3 and pass it to the web application.


Once we paste the new crafted jwt in jwt.io with the invalid user, we see that it works and we get access to the web application, this means we can start testing other web application functionality to see how it reacts what we can do with an invalid user.

the type also says user, but what happens if we change it to admin?

Next up we move on to XSS, lets see what happens if we revisit the success parameter at

localhost/?sucess=...

lets add a payload

`localhost/?sucess=<script>prompt()</script>`

and we see that the payload gets reflected but the text is differet we get

`<[filter]>prompt()</[filter]>`

from here the instructor begins to re write the word script in the URL testing for different ways to bypass the filter.

However it all gets filtered out. So now the instructor says lets try something similar

he tries

`localhost/?sucess=<`

and it is reflected so now we know that the character works so lets move on to something simple

`localhost/?sucess=<h1>hello</h1>`

and the web application reflects back

`<h1>hello</h1>`

so it didnt work, but does it work in other end points of the web application?

is it being filtered or is the filter working through out the entire web application?

lets test it out, 

if we go to 

`localhost/basket?success=<h1>hello</h1>`

Hello is reflected back in larger headers so now we know that the basket endpoint possibly is can be attacked with an XSS payload or template injection!

however if we do something like

`localhost/basket?success=<img src=x onerror=prompt()>`

this gets filtered out in the web application similar to before so lets go to burp suite, find where our payload is being used and lets try different tags/event handlers to see what works, we did this in one of our labs where we used portswigger: https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

and we just simply take all the tags and we head to burp suite, find the request that is the /basket with our payload, send it to intruder, we do clusterbomb attack

in the payload 1 we paste in the tags, and in payload 2 we paste in the event handlers.

in Settings we go to Grep-Match and we add 'filtered'

and we run the attack and now we want to find results that do not have the word filtered in the results

body and onkeypress appears so lets use that

so we go back to the url and do 
`localhost/basket?success=<body onkeypress=prompt()>`

and we load it, and press a key and we get a prompt meaning we got XSS to work!

Next up is Race conditions!

So we go to check out, we check out JuraJima Baseball Cap, we pay and we successfully pay for the baseball cap.

We go back to burpsuite and we are looking for two request

the /checkout and the /shop/basket/update (this inserts things into the basket)

So we send /checkout once to repeater and we send  /shop/basket/update 10 times to repeater.


We do what we did before and we group them together, giving the group a name, and then we send them all to the application using the Send group in parallel (last-byte sync)

We always want to make sure there is something in our basket before we send though, very important!

In this part of the walkthrough he instructor fails to get this race condition to work, and what he advises us to do is to just go through the steps making sure each request in repeater works meaing double check if  /shop/basket/update endpoint in repeater works and if /checkout works in repeater seperately and if they do we can once again group them using one /checkout and mutiple  /shop/basket/update requests to try again.


In doing so we go back to the web application refresh it and we see that one of the orders has a quanity of 16 and total price is 319.84 instead of 19.99 of the singular cap.

so now we confirmed race conditions in the /checkout end point!

This completes the walk through.