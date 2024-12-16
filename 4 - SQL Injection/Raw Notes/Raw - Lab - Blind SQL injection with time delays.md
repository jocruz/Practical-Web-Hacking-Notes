Challenge Lab: [https://portswigger.net/web-security/sql-injection/blind/lab-time-delays](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)

PortSwigger Description:
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.

Hint: 
You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

PortSwigger Solution:

1. Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the `TrackingId` cookie.
2. Modify the `TrackingId` cookie, changing it to:
    
    `TrackingId=x'||pg_sleep(10)--`
3. Submit the request and observe that the application takes 10 seconds to respond.


Instructor Notes:

After reading the Lab description and reading the lab title, we know that we are going to be using Time Delay payloads, so this allows us to narrow down the attacks we will be using.

From here it is important to note that if we didn't know this our attack payload list would be a lot larger and more time consuming.

So we would have to look at the PortSwigger cheat sheet and look at the following:

## Time delays

You can cause a time delay in the database when the query is processed. The following will cause an unconditional time delay of 10 seconds.

|   |   |
|---|---|
|Oracle|`dbms_pipe.receive_message(('a'),10)`|
|Microsoft|`WAITFOR DELAY '0:0:10'`|
|PostgreSQL|`SELECT pg_sleep(10)`|
|MySQL|`SELECT SLEEP(10)`|

The most likely considering the theme of the labs we will be using the MySQL payload, however we will have to go through each one

We will have to change the payloads to make sure we get it working 

```http
GET / HTTP/2
Host: 0aac009403c622f380fd6232002e0094.web-security-academy.net
Cookie: TrackingId=c0Ki5i90865J7TM1; session=9M7dkM26viHC6n0cI94YXNzJ6aQKLol
Sec-Ch-Ua: "Chromium";v="121", "Not A(Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aac009403c622f380fd6232002e0094.web-security-academy.net/filter?category=Gifts
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Priority: u=0, i

```

So for MySQL we would have to change it to ' sleep(10)-- - 

Instructor says we know we should concatenate because the lab hint says so however I do not see that anywhere so I think the lab was either updated or perhaps changed

however the instructor goes on to concanate the payload ' || SLEEP (10)-- -

however we try this payload and it doesnt work so we move onto the postgres version

and we try the following:

Cookie: TrackingId=c0Ki5i90865J7TM1 ' ||pg_sleep(10)-- -;

looking at the right bottom corner we see that the application return the response after 20,100 milliseconds so this is 20 seconds double the time we were expecting!

So we make sure to resend the request, just to make sure that the application did meant to send it in 20,100 milliseconds and we do once again get it back at the 20,100 milliseconds.

So this means that the query might be processed in the initial query and somewhere else in the web application.

It is important to think about, because we have to be careful when testing that our payloads might be processed elsewhere non the less this completed the labs as we managed to do a Blind SQL with time delays. 