
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a broken logic vulnerability in the 2FA process to access the victim’s (Carlos’s) account page.
    
2. **Steps**:
    
    - Investigate the 2FA flow using Burp Suite to understand how the `verify` parameter works.
    - Generate Carlos’s MFA code by tampering with the `verify` parameter in a `GET /login2` request.
    - Brute force the 4-digit MFA code for Carlos using Burp Intruder.
    - Use the captured session cookie to access Carlos’s account page.
3. **Techniques Used**:
    
    - Parameter manipulation to generate MFA codes for another user.
    - Brute-forcing a 4-digit MFA code using Burp Intruder.
    - Session hijacking by injecting the session cookie into the browser.
4. **Key Learning**:
    
    - Flawed logic in 2FA implementations, especially involving predictable or brute-forceable MFA codes, can be exploited.
    - Applications should associate MFA codes with both the user and the session to prevent unauthorized access.

---

## **What to Do Next (Exam Simulation)**

- On an exam, inspect the 2FA process for predictable patterns or tampering opportunities.
- Use Burp Suite's Repeater and Intruder to test and brute force vulnerable 2FA logic.
- Test for session cookies tied to weak user-verification mechanisms.

---

## **Troubleshooting**

- **If brute-forcing the MFA code fails**:
    
    - Verify the payload positions in Burp Intruder.
    - Ensure the character set (`0123456789`) and length are configured correctly.
    - Confirm the `verify` parameter is set to `carlos` in the request.
- **If the session cookie doesn’t work**:
    
    - Confirm you are injecting the correct session cookie into the browser.
    - Double-check the request and response to ensure the session cookie corresponds to the victim (Carlos).

---

## **Key Takeaways**

- The `verify` parameter is critical to generating MFA codes and can be manipulated.
- MFA codes should be user-specific and session-specific to prevent exploitation.
- 2FA implementations with predictable or brute-forceable codes are highly insecure.

---

## **Step 1: Log In to Your Account**

1. Log in with your own credentials (`wiener:peter`) and complete the 2FA step.
2. Intercept the login process in Burp Suite and inspect:
    - The `POST /login` request.
    - The `POST /login2` request, which includes the `verify` parameter and the `mfa-code`.

---

## **Step 2: Investigate the 2FA Process**

1. Observe the cookies in the intercepted requests:
    - **Example**: `Cookie: verify=wiener; session=rQJbtZy0DedQsXg0psIKs88QIMboLft`.
    - Note how the `verify` cookie corresponds to the username.
2. Highlight the `/login2` request in Burp Suite to identify the `mfa-code` parameter (e.g., `mfa-code=0962`).

---

## **Step 3: Generate Carlos’s MFA Code**

1. Send the `GET /login2` request to **Repeater**.
2. Modify the `verify` parameter from `wiener` to `carlos`.
3. Send the modified request. This action generates an MFA code for Carlos.

---

## **Step 4: Brute Force Carlos’s MFA Code**

1. Locate the `POST /login2` request and send it to **Intruder**.
2. Configure payloads:
    - Set the `verify` parameter to `carlos`.
    - Place a payload position on the `mfa-code` parameter (e.g., `0962`).
    - Switch the payload type to **Brute Forcer**.
3. Configure the character set to `0123456789` with a minimum and maximum length of `4`.`
![[Pasted image 20241209155307.png]]

---

## **Step 5: Identify the Valid MFA Code**

1. Start the Intruder attack.
2. Look for a response with a status code `302` (indicating a successful MFA verification).
3. Identify the valid MFA code (e.g., `0262`).

---

## **Step 6: Capture the Session Cookie**

1. Switch to the **Response** tab in Burp Suite for the successful request.
2. Copy the session cookie issued after brute-forcing the MFA code.

---

## **Step 7: Inject the Cookie in the Browser**

1. Open the browser developer tools and go to the **Application** tab.
2. Replace the existing session cookie with the one obtained from Burp Suite:
    - Delete the existing `verify=wiener` and `session` cookies.
    - Inject the captured session cookie corresponding to Carlos.
3. Save the changes.
![[Pasted image 20241209155123.png]]
---

## **Step 8: Access Carlos’s Account Page**

1. Navigate to `/my-account?id=carlos` in the browser.
2. Confirm that you have successfully accessed Carlos’s account page.
3. **Lab Complete!**
![[Pasted image 20241209155147.png]]
---

#authentication #sessionmanagement #2FAbypass #logicalflaws #cookie