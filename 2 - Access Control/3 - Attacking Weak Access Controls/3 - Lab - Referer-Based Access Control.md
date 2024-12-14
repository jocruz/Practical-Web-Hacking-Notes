
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a weak **_Referer header-based access control_** to promote a non-admin user (Wiener) to an administrator role by reusing an admin request with the non-admin user's session cookie.
    
2. **What Makes This Vulnerable?**:
    
    - The application relies on the **_Referer header_** to control access to administrative functionality. This is a **_weak mechanism_** because the Referer header can be manipulated or omitted entirely, allowing attackers to bypass restrictions.
    - By replacing the session cookie and modifying the username parameter, any user can escalate their privileges to admin.
      
3. **Steps**:
    
    - Log in as the administrator to capture the promotion request.
    - Replace the session cookie in the captured request with the non-admin user's session cookie.
    - Replay the request to elevate the non-admin user's privileges to admin.
      
4. **Techniques Used**:
    
    - **_Session cookie manipulation_** to impersonate another user in the administrative request.
    - Replaying captured administrative requests to exploit missing validation of the user's role.
      
5. **Key Learning**:
    
    - **_Referer header-based access control_** is not secure and should not be used for sensitive actions.
    - Administrative actions should always validate the **_user’s role and identity_** on the server side.

---

## **What to Do Next (Exam Simulation)**

- When testing for **_access control vulnerabilities_**, always:
    - Analyze requests for sensitive actions to identify weak mechanisms like Referer headers.
    - Test session and role tampering to bypass restrictions.
      
- Document each step to confirm **_successful exploitation_**.

---

## **Troubleshooting**

- **_If the promotion fails_**:
    - Verify that you are replacing the session cookie correctly with the non-admin user’s session.
    - Ensure the request includes all necessary headers (except the Referer header if testing its absence).
      
- **_If the application doesn’t respond_**:
    - Resend the request in Burp Suite Repeater to account for potential lost traffic.

---

## **Key Takeaways**

- Access control mechanisms relying on **_client-controlled headers (e.g., Referer)_** are inherently weak.
- Session cookies should always be validated against the **_user’s role and permissions_** on the server side.
- Browser extensions like **_Firefox Multi-Account Containers_** can streamline testing by enabling multiple sessions simultaneously.

---

## **Step 1: Log In as Administrator and Capture the Promotion Request**

1. Log in using the provided **administrator credentials** (`administrator:admin`).
2. Navigate to the **Admin Panel** via the hyperlink.
3. Promote Carlos to admin, then downgrade Carlos back to a normal user:
    - This generates two GET requests:
        
        ```plaintext
        /admin-roles?username=carlos&action=upgrade
        /admin-roles?username=carlos&action=downgrade
        ```
        
4. Send the **upgrade request** (`/admin-roles?username=carlos&action=upgrade`) to **_Burp Suite Repeater_**.

---

## **Step 2: Test Request Behavior**

1. Remove the **_Cookie: session_** header from the captured request in Repeater.
2. Send the request. The response will be **HTTP 401 Unauthorized**, as expected.

---

## **Step 3: Log In as Wiener and Replace Session Cookie**

1. Open a **_private/incognito browser window_** and log in as Wiener using the credentials (`wiener:peter`).
2. Use Burp Suite to capture the **_GET request for /my-account?id=wiener_**.
3. Extract Wiener’s **_session cookie_** from the request.
4. Replace the admin session cookie in the captured **_upgrade request_** with Wiener’s session cookie in Repeater.

---

## **Step 4: Replay the Request**

1. Send the modified request in Repeater.
2. If no response is received, resend the request.
    - The expected response is **HTTP 302 Found**, indicating the request was processed.
3. Follow the redirection. The response will show **HTTP 401 Unauthorized**, which is normal.

---

## **Step 5: Verify Success**

1. Log out of Wiener’s account and log back in as Administrator.
2. Navigate to the **Admin Panel** to confirm that Carlos has been promoted to admin.

---

## **Step 6: Promote Wiener to Admin**

1. Log back in as Wiener and repeat the process:
    - Capture Wiener’s **new session cookie** (via Burp Suite or the browser console’s **Storage** tab).
    - Replace the session cookie in the **_upgrade request_** with Wiener’s updated session cookie.
    - Change the `username` parameter in the request body to `wiener`:
        
        ```plaintext
        /admin-roles?username=wiener&action=upgrade
        ```
        
2. Send the request. The response will be **HTTP 302 Found**.
3. Verify in the lab interface that Wiener has been successfully promoted to admin.

---

## **Using Firefox Multi-Account Containers**

### **What are Multi-Account Containers?**

- **_Firefox Multi-Account Containers_** is a browser extension that allows users to maintain **_separate browser sessions_** in different tabs or windows.
- Each container acts as an isolated browser instance, allowing different cookies and session data in the same browser.

---

### **When to Use Them**

- Use containers during **_multi-session testing_**:
    - Simulating different user roles (e.g., admin vs. user).
    - Avoiding the need to repeatedly log in and out of accounts.
    - Maintaining distinct session cookies for simultaneous testing of vulnerabilities like **_access control_**.

---

### **How to Use Them**

1. Install the **_Firefox Multi-Account Containers_** extension.
2. Open different tabs and assign them to unique containers (e.g., **blue for admin**, **orange for user**).
3. Log into the web application with different user roles in each container.
4. Test requests from different accounts simultaneously without interference.
    - Example: Use the admin account in the blue container to capture requests, while using the user account in the orange container to manipulate session cookies.

---

### **Why Use Them?**

- Speeds up testing for **_multi-user vulnerabilities_**, especially when testing actions that involve session cookies or role escalation.
- Helps avoid mistakes caused by overwriting cookies or logging out of accounts unnecessarily.

---

## **Weak Access Controls in the Lab**

- **Access Control Mechanism**:
    - The lab uses the **_Referer header_** to control access to administrative functionality.
- **Why This is Weak**:
    - The **Referer header is client-controlled** and can be omitted or manipulated.
    - No server-side validation of the **_user’s role_** occurs for sensitive actions like promoting users to admin.
- **Mitigation**:
    - Implement **_server-side role validation_** for all administrative actions.
    - Avoid relying on **_client-provided headers_** (like Referer) for sensitive functionality.

---

#accesscontrol #Refererheader #FirefoxContainers #lab #roleescalation