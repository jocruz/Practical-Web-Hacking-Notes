https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables
PortSwigger Description:

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

The database contains a different table called `users`, with columns called `username` and `password`.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.

PortSwigger Solution:
1. Use Burp Suite to intercept and modify the request that sets the product category filter.
2. Determine the [number of columns that are being returned by the query](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns) and [which columns contain text data](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text). Verify that the query is returning two columns, both of which contain text, using a payload like the following in the category parameter:
    
    `'+UNION+SELECT+'abc','def'--`
3. Use the following payload to retrieve the contents of the `users` table:
    
    `'+UNION+SELECT+username,+password+FROM+users--`
4. Verify that the application's response contains usernames and passwords.

How I solved it:

I logged into the lab, I saw that where was categories on the web application, following the lab I went ahead and clicked through the categories and I generated traffic so that Burp Suite can capture them. Went to Burp Suite and I found the requests that had the following:

Method:GET
URL: filter?category=Clothing%2c+shoes+and+accessories

I reviewed the rest of the request but based on the description of the lab I knew that I had to place a payload in that category arguement. I went ahead and went to 
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#union-based-injection
and I copied the following payload:

1' UNION SELECT username, password FROM users --

so the header now has this in the request:
GET /filter?category=Clothing%2c+shoes+and+accessories1' UNION SELECT username, password FROM users -- HTTP/2

I got an 200 response and saw this in the response:

                       <tr>
                            <th>wiener</th>
                            <td>e0rhde6r01n2svvmpp42</td>
                        </tr>
                        <tr>
                            <th>carlos</th>
                            <td>nd76dn00fy7xpm8m1ghe</td>
                        </tr>
                        <tr>
                            <th>administrator</th>
                            <td>r08fng6gvgdcoxnsfsj5</td>

and from there I just took the Administrator credentials and logged in completing the lab.


Instructors Solution:

Same as before loaded the lab and generated traffic, went to a different GET request with the  URL: filter?category=Pets

From here he sent it to Intruder, and put a positional marker on Pets and went ahead in intruder went to payloads and loaded a payload from the following

/usr/share/seclists/Fuzzing/SQLi

And he chose the .txt file named Generic-SQLi.txt

the payloads load, and its worth noting that some of the payloads append two statements so its better to go back to intruder and modify the request to make sure we have a valid payload first before we add in our SQLi payload

so he goes ahead and modifies the payload before starting the attack:

URL: filter?category=Pets$Pets$

The $ represents the positional marker

however he then says its worth doing it but its not necessary for this lab and deletes the valid payload and keeps the positional marker around the valid payload 

URL: filter?category=Pets


He then says in the paylods tab in intruder we have to make sure that we are URL encoding the characters so we MUST have the check box "URL-encode these characters" checked off in the section "Payload Encoding" other wise a lot of HTTP requests will be broken


we start our attack,

Something to take note of is the character length, if the application was NOT subject to SQL injection then we would expect very similar lengths across our responses. There can be some variation some minor differences. So filtering by the length we see that there is a payload that does return with a length of 27828 and the rest of the lengths are around 3000. 

If we go back to Proxy, HTTP History in Burp Suite, our initial request was about 2763 so this is an interest indicator that all the results were returned


We send our payload that does return a high character length in the attack window and we do ctrl+r to send it to repeater and the payload that did work was hi' or 'a'='a


The instructor simplifies the payload does ctrl+shift+u to decode it, we get our payload, we remove hi from the payload  and we get ' or 'a'='a

we recencode it using ctrl+u and we send it using repeater, and we get 2700 

and if we create a false statement ' or 'a'='b we get a content length of 3818

So here we use true false statement that we do have SQL injection. 

We fuzzed the application and then we manually verified the attack.


Now what we do is we head over to our terminal,

we copy the URL of the lab that contains the filter so 

we do

$ sqlmap 'https://0abb00ac0399d7e0809f1cfa00e0001b.web-security-academy.net/filter?category=Pets' --dbs


We say Y to testing connection to the target URL 

we see that our results that the back-end DBMS might be PostgreSQL, and we will say Y to skipping the rest of the tests of other Databases, and say Yes to include all tests for PostgreSQL extending provided level (1) and risk (1) values

level one does http request and parameters, if we need to test cookies we have to increase the risk.

The output shows that the GET parameter 'category' is vulerable, and do we want to keep testing others, we say N

and we get a payload which is a UNION attack payload, and we see that the output shows us that the available database name is public

we stop the attack/command and now we do 

$ sqlmap 'https://0abb00ac0399d7e0809f1cfa00e0001b.web-security-academy.net/filter?category=Pets' -D public --tables --batch

it will take all of the default answers to the questions using the --batch flag

and now it returns the database named public and the tables named products and users.

so now we change the command again using

$ sqlmap 'https://0abb00ac0399d7e0809f1cfa00e0001b.web-security-academy.net/filter?category=Pets' -D public -T users --batch --dump

we use --dump to dump all of the data from the table.

We get the table of users

and the table contains the username and passwords which is the Administrator username and the Administrator password lzr7vlf5vf8johylrz93

we go back to the lab and use those credentials to log in and we complete the lab.