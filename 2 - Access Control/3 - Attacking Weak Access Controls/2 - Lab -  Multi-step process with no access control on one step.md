
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a **_multi-step process with missing access controls_** to promote a non-admin user (Wiener) to an administrator role by reusing an admin request with a tampered session cookie.
    
2. **What Makes This Vulnerable?**:
    
    - The application fails to enforce proper **_access control_** on one step of the multi-step process (the promotion request), allowing non-admin users to perform administrative actions by reusing admin requests with their session cookies.
3. **Steps**:
    
    - Log in as the administrator to capture the promotion request.
    - Use the captured request to modify another user’s role.
    - Replace the session cookie with the non-admin user’s session to elevate their role to admin.
4. **Techniques Used**:
    
    - **_Session cookie manipulation_** to impersonate the non-admin user in the administrative request.
    - Replaying captured administrative requests to exploit missing access controls.
5. **Key Learning**:
    
    - **_Access control must be validated server-side_** at every step of multi-step processes, not just at the initial step.
    - Administrative actions should verify both the role and identity of the user initiating the request.

---

## **What to Do Next (Exam Simulation)**

- When testing for **_multi-step access control vulnerabilities_**, always:
    - Intercept and analyze requests for administrative actions.
    - Test for session or role tampering to bypass restrictions.
- Document each step carefully to avoid **_false positives_** and ensure reproducibility.

---

## **Troubleshooting**

- **_If the promotion fails_**:
    - Verify that you are replacing the session cookie correctly with the non-admin user’s session.
    - Ensure the request body contains the correct username (e.g., `username=wiener`).
- **_If 401 Unauthorized is returned after following the redirection_**:
    - This is expected and does not indicate failure; check the lab interface to confirm the role change.

---

## **Key Takeaways**

- **_Multi-step processes_** are prone to access control vulnerabilities if not consistently validated.
- Session cookies should always be tied to **_both the role and identity_** of the user initiating the request.
- Tools like Burp Suite Repeater are essential for **_replaying and modifying captured requests_** to test for such vulnerabilities.

---

## **Step 1: Log In as Administrator and Capture the Promotion Request**

1. Log in using the provided **administrator credentials** (`administrator:admin`).
2. Navigate to the **Admin Panel** via the hyperlink.
3. Promote Carlos to an administrator:
    - This generates a **POST request to /admin-roles** with the following body:
        
        ```plaintext
        action=upgrade&confirmed=true&username=carlos
        ```
        
4. Downgrade Carlos back to a normal user to ensure the request is reusable.
5. Send the promotion request to **_Burp Suite Repeater_** for modification and replay.

---

## **Step 2: Log In as Wiener and Replace Session Cookie**

1. Open an **_incognito/private browser window_** and log in as Wiener using the credentials (`wiener:peter`).
2. Use Burp Suite to capture the **_GET request for /my-account?id=wiener_**.
3. Extract Wiener’s **_session cookie_** from the request.

---

## **Step 3: Modify and Replay the Captured Promotion Request**

1. In Repeater, replace the admin session cookie with Wiener’s session cookie in the captured **POST /admin-roles** request.
2. Modify the **username** parameter in the request body:
    - Change:
        
        ```plaintext
        action=upgrade&confirmed=true&username=carlos
        ```
        
    - To:
        
        ```plaintext
        action=upgrade&confirmed=true&username=wiener
        ```
        
3. Send the modified request in Repeater.

---

## **Step 4: Confirm Success**

1. Observe the **_HTTP/2 302 Found_** response, indicating the promotion request was processed.
2. **Follow the redirection** in Repeater (optional).
    - If a **401 Unauthorized** response is returned, this is expected and does not indicate failure.
3. Log back in as Wiener and check the lab interface to confirm Wiener’s role has been upgraded to admin.

---

## **Step 5: Complete the Lab**

1. Verify in the lab interface that Wiener is now an administrator.
2. The lab objective is met:
    - **_“To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.”_**

---

## **Core Concept: Why This Vulnerability Exists**

- **_Access control validation_** is missing for the promotion request, allowing any user with a valid session cookie to send administrative commands.
- By replacing the session cookie and modifying the username parameter, the server incorrectly processes the request as if it were made by an administrator.
- Proper mitigation would enforce **_role-based access control_** (RBAC) at every step of the process, ensuring that only users with the appropriate privileges can perform administrative actions.

---

#multistepaccesscontrol #accesscontrol #IDOR #lab #sessionmanipulation #cookie