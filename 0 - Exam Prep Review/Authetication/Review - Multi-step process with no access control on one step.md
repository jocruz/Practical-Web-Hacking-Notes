
---

## 💡 Quick Overview

Because the web application does not verify the session cookie's permissions, a non-admin user can exploit admin functionality by replacing the admin's session cookie with their own in a captured admin request. This allows unauthorized users, like Wiener, to perform privileged actions such as promoting themselves to an admin role.

---

## 🛠️ Steps to Complete the Lab

1. **Log In as Administrator**:
    
    - Use `administrator:admin` credentials.
    - Navigate to the **Admin Panel** and promote Carlos to an admin.
    - Capture the promotion request (`POST /admin-roles`) in Burp Suite and send it to Repeater.
2. **Log In as Wiener**:
    
    - Use `wiener:peter` credentials in a private browser window.
    - Capture a request from Wiener’s session (e.g., `GET /my-account?id=wiener`) to retrieve Wiener’s session cookie.
3. **Replace the Session Cookie**:
    
    - In the captured promotion request in Repeater, replace the admin’s session cookie with Wiener’s session cookie.
    - Modify the request body to:
        
        ```plaintext
        action=upgrade&confirmed=true&username=wiener
        ```
        
4. **Replay the Request**:
    
    - Send the modified request and observe a `302 Found` response.
    - Log back in as Wiener to confirm the role change.
5. **Complete the Lab**:
    
    - Verify Wiener is now an administrator in the lab interface.

---

## ❓ Why This is an Access Control Issue

The application fails to validate permissions associated with the session cookie. When the admin’s session cookie is replaced with Wiener’s:

- The server processes the request without re-checking whether Wiener has admin privileges.
- This inadequate access control allows any valid session to perform privileged actions based on request structure alone, violating role-based access control (RBAC) principles.

---
