
---

## **💡 Quick Overview**

Access the unprotected admin panel endpoint disclosed in the `robots.txt` file. Use this endpoint to delete the victim user, Carlos, without requiring authentication or authorization. This demonstrates an Insecure Direct Object Reference (IDOR) vulnerability where administrative functionality is exposed due to lack of access control.

---

## **🛠️ Steps to Complete the Lab**

1. **Locate the Admin Panel**:
    
    - Append `/robots.txt` to the lab URL in your browser.
    - Identify the hidden admin panel path (e.g., `/administrator-panel`) in the `robots.txt` file.
2. **Access the Admin Panel**:
    
    - Replace `/robots.txt` with `/administrator-panel` in the URL.
    - Navigate to the admin panel, confirming that it loads without requiring authentication.
3. **Delete Carlos**:
    
    - In the admin panel, locate the option to delete users.
    - Select Carlos and delete the user.
    - Confirm the lab is complete.

---

## **❓ Why This is an Access Control Issue**

This vulnerability arises because:

1. **No Authentication or Authorization**: The admin panel and its functionality are accessible to any user without requiring credentials or role-based permissions.
2. **Reliance on Obscurity**: Hiding the admin panel in `robots.txt` does not provide security, as the file is publicly accessible and easily enumerated.
3. **Improper Protection of Actions**: Actions like deleting users rely solely on accessing the endpoint, allowing unauthorized users to perform administrative operations.

---
