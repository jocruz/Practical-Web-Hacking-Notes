
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a Broken Function Level Authorization (BFLA) vulnerability by manipulating the `id` parameter in the URL to access another user's sensitive information (API key).
    
2. **Steps**:
    
    - Log in with your credentials and inspect the account page URL, which includes the `id` parameter tied to your username.
    - Send the request to Burp Repeater and modify the `id` parameter to the victim's username (`carlos`).
    - Access the victim’s account page, retrieve their API key, and submit it to complete the lab.
3. **Techniques Used**:
    
    - Parameter tampering to test for access control vulnerabilities.
    - Session misuse to exploit the lack of proper authorization checks.
4. **Key Learning**:
    
    - Proper access control must be enforced on server-side requests to prevent unauthorized access to sensitive resources.
    - Session cookies should be tied to specific user accounts and not allow arbitrary parameter manipulation.

---

## **What to Do Next (Exam Simulation)**

- On an exam, inspect all user-controlled parameters in GET and POST requests.
- Use tools like Burp Suite Repeater to modify parameters and test access control.
- If a vulnerability is found, document it thoroughly, including steps to reproduce and its impact.

---

## **Troubleshooting**

- **If accessing the victim’s account fails**:
    
    - Ensure the `id` parameter is correctly modified to `carlos`.
    - Verify that the session cookie is included in the request.
    - Double-check the HTTP method and endpoint (e.g., `GET /my-account`).
- **If the API key is not visible**:
    
    - Check the response in Repeater’s **Render** tab to confirm you are viewing the correct user’s page.
    - Ensure you have modified the `id` parameter to a valid username.

---

## **Key Takeaways**

- The vulnerability arises because the server uses the `id` parameter in the URL to identify the user instead of validating against the session cookie.
- Sessions must be scoped to a single user, and the server must enforce strict access controls on sensitive endpoints.
- Broken Function Level Authorization vulnerabilities can be devastating, allowing attackers to access or modify another user’s data.

---

## **Step 1: Log In and Inspect the URL**

1. Log in using the provided credentials (`wiener:peter`).
2. Navigate to your account page and note the URL structure:
    - **Example**: `/my-account?id=wiener`.
3. Observe the page contents:
    - **Username**: `wiener`.
    - **API Key**: `iNmKbmCgvGSoRt...`.

---

## **Step 2: Send the Request to Repeater**

1. In Burp Suite, find the `GET /my-account` request in the **HTTP history**.
2. Send the request to **Repeater** (Ctrl+R).
3. Observe the `id` parameter in the request:
    - **Example**: `GET /my-account?id=wiener`.

---

## **Step 3: Test for Access Control Vulnerabilities**

1. In Repeater, modify the `id` parameter:
    - Change `wiener` to `carlos`.
    - **Example**: `GET /my-account?id=carlos`.
2. Send the modified request and review the response.
3. In the **Render** tab, confirm that you now have access to Carlos’s account page and API key:
    - **API Key**: `mFuRzWx...`.

---

## **Step 4: Complete the Lab**

1. Copy the API key from Carlos’s account page.
2. Submit the API key to PortSwigger to complete the lab.

---

## **Key Learning Point**

The server improperly uses the `id` parameter for authorization. The session cookie should tie a user to specific actions, and server-side validation should ensure that sensitive operations are only performed by authorized users.

---

## **Additional Example (BFLA Finding in PWPA Certification Exam)**

**Technical Finding:**

**Discovered a Broken Function Level Authorization (BFLA) vulnerability.**

- Using two accounts (`John` and `Test`), tested the `DELETE` functionality on the `/vault/delete` endpoint.
- Steps:
    1. Deleted a vault entry using John's account and session cookie:
        - **Example Cookie**: `connect.sid=s%3AcFTQPiBeRrDhNd9aZ512xr5MD-CUFFSs...`.
    2. Logged into Test’s account and captured its session cookie:
        - **Example Cookie**: `connect.sid=s%3Ag8EdtyM8PDmFEAR_cZr9UcVK8tfX5bxy...`.
    3. Replaced John’s session cookie with Test’s cookie in the `DELETE` request.
        - Result: The server responded with `HTTP/1.1 200 OK` and confirmed the deletion.

**Impact:**  
Lower-privileged users could perform actions meant for higher-privileged users, demonstrating improper authorization checks.

---

#accesscontrol #idparameter #BFLA #authorization #lab 