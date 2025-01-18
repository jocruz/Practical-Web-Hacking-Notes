Here are your updated notes with **all screenshots included** as per your instructions:

---

# Lab: Brute-forcing a Stay-Logged-In Cookie

## Objective

Understand if a token appears random and use Burp Suite's **Sequencer** to analyze its randomness. Exploit the vulnerability in the stay-logged-in cookie to gain access to the victim’s account.

---

## Lab Steps

### 1. **When to Use Sequencer**:

- Use Sequencer if:
    - The application appears to use its own session management.
    - The session token is short, repetitive, or doesn't appear random.

---

### 2. **Lab Description**:

- The lab allows users to stay logged in even after closing their browser.
- The stay-logged-in cookie is vulnerable to brute-forcing.
- **Credentials**:
    - Your credentials: `wiener:peter`
    - Victim's username: `carlos`
    - [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

---

### 3. **Initial Observations**:

- Log in to the "My Account" page using the provided credentials.
- Select the "Stay logged in" checkbox.
- Compare requests with and without the checkbox selected to observe differences.

---

### 4. **Request Analysis**:

- Check the **/login** request in Burp Suite **Proxy Tab**:
    
    ```html
    username=wiener&password=peter&stay-logged-in=on
    ```
    
- Response headers include:
    
    ```html
    Set-Cookie: stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw
    Set-Cookie: session=urieAQvk9gEnrKajx77MEzOZtORFTlQs;
    ```
    

---

### 5. **Repeater Observations**:

- Send the request to **Repeater**.
- Observations:
    - `stay-logged-in` cookie is **static**.
    - `session` cookie changes with each request.
- Implications:
    - The static cookie might not expire, could be predictable, and potentially exploitable.

---

### 6. **Static Cookie Analysis**:

#### A. **Sequencer Analysis**:

1. Send the request to **Sequencer**.
2. Configure **Token Location within Response**:
    - Highlight the dynamic token to define its location.
    - Start **Live Capture**.
    - Analyze the captured tokens:
        - Results for `session` cookie:
            - **Excellent randomness** (149 bits of entropy).
            - No red flags in the **Overall Analysis** or **Character-Level Analysis**.

#### B. **Decoder Analysis**:

- Decode the static `stay-logged-in` cookie using Burp's **Decoder Tab**:
    
    ```plaintext
    d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw
    ```
    
- Decoded value:
    
    ```plaintext
    wiener:51dc30ddc473d43a6011e9ebba6ca770
    ```
    
- Confirm the decoded value includes:
    - Username: `wiener`.
    - MD5 hash of the password.

#### C. **Hackvertor Analysis**:

- Use Burp's **Hackvertor** extension to decode:
    
    ```plaintext
    <@d_base64>d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw<@/d_base64>
    ```
    
- Output:
    
    ```plaintext
    wiener:51dc30ddc473d43a6011e9ebba6ca770
    ```
    
- **Hackvertor Screenshot**: ![[Pasted image 20241206203932.png]]

---

### 7. **Sequencer Settings for Static Cookie**:

1. Go to **Token Handling**.
2. Enable **Base64-decode before analyzing**.
3. Start live capture again.
4. Analysis Results:
    - Overall Quality: **Extremely Poor**.
    - Character-Level Analysis: **All red**, randomness below 0.0001%.
    - **Sequencer Setting Screenshot**: ![[Pasted image 20241206204225.png]]
    - **Character Analysis Screenshot**: ![[Pasted image 20241206205527.png]]

---

### 8. **Simplify the Request**:

- Remove the `id=wiener` parameter and `session` cookie from the request.
- Confirm functionality remains unaffected:
    - Logged-in state persists.

---

### 9. **Brute Forcing with Intruder**:

1. Send the simplified request to **Intruder**.
2. Configure:
    - **Position Marker**: Around the `stay-logged-in` cookie.
    - **Payload**:
        - Use the provided `passwords.txt` wordlist.
    - **Payload Processing Rules**:
        1. Add Rule: **Hash -> MD5**.
        2. Add Rule: **Add Prefix -> "carlos:"**.
        3. Add Rule: **Encode -> Base64**.
    - **Payload Processing Rules Screenshot**: ![[Pasted image 20241206212328.png]]
3. Start a **Sniper Attack**.
4. Results:
    - Identify responses with:
        - Status Code: `200`.
        - Content Length: `3346`.

---

### 10. **Validate Payload**:

- Copy the successful payload and replace the `stay-logged-in` cookie in **Repeater**.
- Send the simplified request:
    
    ```plaintext
    GET /my-account HTTP/2
    Host: target.com
    Cookie: stay-logged-in=<PAYLOAD>
    ```
    
- **Response Render** confirms access to Carlos’s account, completing the lab.

---

### 11. **Hash Validation in Kali Linux**:

- Verify the MD5 hash using Kali Linux:
    
    ```bash
    echo -n 'peter' | md5sum
    ```
    
- Output confirms the hash matches:
    - **Screenshot of Kali Linux Command**: ![[Pasted image 20241206205830.png]]

---

## Observations

- The `stay-logged-in` cookie is predictable, using `username:md5(password)`.
- Decoding Base64 and analyzing token randomness are crucial steps in identifying weak session management implementations.
- Simplifying requests reduces complexity during brute force attacks.

---

## Tools Used

- **Burp Suite**: Proxy, Repeater, Sequencer, Intruder, Decoder, Hackvertor.
- **CyberChef**: For validating decoded values.
- **Kali Linux command : md5sum**: For MD5 hash verification.

---

## Related Notes

- [[Analyzing Session Tokens for Randomness]]  
    _(Techniques to evaluate token randomness and identify weaknesses in session management.)_
- [[Brute Forcing Weak Tokens]]  
    _(Steps to exploit predictable session cookies for account compromise.)_

---

## Next Steps

1. Apply brute-forcing techniques to other session management implementations.
2. Test defenses such as rate-limiting, account lockout mechanisms, and other security controls.

---

#lab #burpsuite #intruder #sequencer #authentication
