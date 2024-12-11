
# Steps to Complete the Lab: Username Enumeration via Response Timing

---
## **High-Level Summary of the Lab**

1. **Objective**: Identify valid usernames and passwords for a login form using header manipulation and response timing.
    
2. **Steps**:
    
    - **Header Manipulation**: Used multiple headers to bypass lockout mechanisms and pinpoint the `X-Forwarded-For` header as the critical bypass tool.
    - **Username Enumeration**: Tested usernames with a long password string to exploit response time differences and identify the correct username.
    - **Password Cracking**: Used the discovered username with payload-based password attacks to identify the correct password.
    - **Login**: Intercepted the login request, applied header modifications, and successfully bypassed lockout mechanisms to log in.
3. **Techniques Used**:
    
    - Timing analysis to identify valid credentials.
    - Payload-based testing with the Pitchfork attack in Burp Suite Intruder.
4. **Key Learning**:
    
    - Application vulnerabilities like reliance on user-controlled headers and predictable response behavior can be exploited for enumeration and bypass attacks.
    - Proper configuration, input validation, and rate-limiting can mitigate such attacks.
## What to Do Next (Exam Simulation)

- On an exam, immediately consider spoofing IP-related headers if faced with a lockout.
- Use timing-based enumeration to identify valid usernames quickly.
- Leverage Intruder’s sorting and auto scroll features to streamline analysis.
- Once a valid username is found, transition to password brute forcing or credential stuffing strategies.

## Troubleshooting

- **If still locked out**:  
  - Try a different header (e.g., from `X-Real-IP` to `X-Forwarded-For`).
  - Ensure session cookies are maintained in each request.
- **If no timing differences emerge**:  
  - Increase the number of attempts.
  - Check for network latency or consider alternative enumeration techniques.

## Key Takeaways
- Timing differences reveal valid usernames.
- Spoofing IP-related headers (e.g., **X-Forwarded-For**) bypasses brute force protections.
- Automated tools like Burp Intruder, combined with methodical testing, are essential for efficient enumeration.

## Step 1: Checklist of Headers
Use the following headers to manipulate requests:
- `X-Real-IP`
- `X-Forwarded-For`
- `X-Originating-IP`
- `Client-IP`
- `True-Client-IP`

Add the headers with the same value:
```plaintext
X-Real-IP: 1.2.3.4
X-Forwarded-For: 1.2.3.4
X-Originating-IP: 1.2.3.4
Client-IP: 1.2.3.4
True-Client-IP: 1.2.3.4
```

---

## Step 2: Locate the Login Request
1. Attempt to log in to the web application.
2. Find the request in **HTTP history** (e.g., `POST /login`).
3. Send this request to **Repeater** for modification and testing.

---

## Step 3: Test Lockout Behavior
1. Add all headers listed above to the request in Repeater.
2. Fail multiple login attempts to trigger a **lockout**.
3. Modify the last digit of the IP in all headers (e.g., `1.2.3.4` → `1.2.3.5`) and resend the request.
   - Confirm that you are unblocked.
4. Repeat this process by incrementing the IP further (e.g., `1.2.3.5` → `1.2.3.6`).
5. **Key Action:** Remove one header at a time to identify which one is responsible for unblocking.  
   - **Result:** The `X-Forwarded-For` header is responsible for bypassing lockouts.

---

## Step 4: Lockout Testing with the Critical Header
1. Lock yourself out again, using only the `X-Forwarded-For` header in the request.
2. Increment the last digit of the IP (e.g., `1.2.3.6` → `1.2.3.7`) to confirm that modifying this header unblocks you.
3. **Conclusion:** The `X-Forwarded-For` header controls the lockout mechanism.

---

## Step 5: Configure Intruder for Username Enumeration
1. Send the modified request with the `X-Forwarded-For` header to Intruder.
2. Place positional markers on:
   - The last digit of the `X-Forwarded-For` IP.
   - The `username` parameter.
3. Switch the attack type to **Pitchfork**.

---

## Step 6: Set Intruder Payloads
1. Go to the **Payloads** tab:
   - **Payload 1:** Numbers from `10` to `110`.
   - **Payload 2:** Use the `username.txt` file provided by PortSwigger (contains 101 usernames).

---

## Step 7: Use a Long Password String
1. In the request, set the password parameter to a long string, such as:
   ```plaintext
   testtesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttest...
   ```
2. **Reason:** 
   - If the username is incorrect, the application rejects it immediately, resulting in a quick response time.
   - If the username is correct, the system spends time hashing the password (e.g., with bcrypt) to verify it against the database.
   - This delay allows you to identify the correct username based on response time.

---

## Step 8: Start the Attack and Analyze Results
1. Start the attack in Intruder.
2. Add the columns **Response Received** and **Response Completed** to the results.
3. Identify the username with a significantly longer response time (e.g., `oracle` with `1500ms` vs. others under `30ms`).
4. **Result:** The username `oracle` is valid.

---

## Step 9: Configure Intruder for Password Enumeration
1. Send the updated request to Intruder.
2. Place positional markers on:
   - The `password` parameter.
   - Another digit in the `X-Forwarded-For` header (e.g., `1.2.x.4`).
3. Set payloads:
   - **Payload 1:** Numbers from `10` to `110`.
   - **Payload 2:** Use the `password.txt` file provided by PortSwigger.

---

## Step 10: Identify the Correct Password
1. Start the attack in Intruder.
2. Look for a **302 Found** response in the results, indicating a successful login attempt.
3. **Result:** Identify the correct password for the previously discovered username.

---

## Step 11: Log in with the Credentials
1. Attempt to log in using the identified username and password.
2. **Intercept the login request** using Burp Suite.  
   - **Reason:** Since the application has locked you out, intercepting allows you to modify the request to bypass the lockout mechanism.
3. Modify the intercepted request:
   - Replace the username and password with the valid credentials.
   - Add the `X-Forwarded-For` header and change the IP to a new number (e.g., `1.x.3.4`).
4. Forward the modified request through Burp Suite to send it to the server.

---

## Step 12: Confirm Completion
If the login is successful, the lab is complete!


#authentication #lab #responsetiming #ratelimit
