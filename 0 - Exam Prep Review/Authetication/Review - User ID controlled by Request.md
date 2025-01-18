
---

## **💡 Quick Overview**

Modify the `id` parameter in the URL to the victim's username (`carlos`) while logged in with your own credentials. This allows you to access the victim's account page and retrieve their API key without proper authorization.

---

## **🛠️ Steps to Complete the Lab**

1. **Log In and Inspect the URL**:
    
    - Log in with `wiener:peter`.
    - Navigate to `/my-account` and note the `id` parameter in the URL (e.g., `/my-account?id=wiener`).
2. **Send the Request to Repeater**:
    
    - Locate the `GET /my-account` request in Burp Suite and send it to Repeater.
3. **Modify the `id` Parameter**:
    
    - Change the `id` parameter from `wiener` to `carlos` in Repeater.
    - Send the modified request and review the response.
4. **Retrieve the Victim’s API Key**:
    
    - Confirm access to Carlos’s account page in the response and copy the API key.
5. **Complete the Lab**:
    
    - Submit the API key to complete the lab.

---

## **❓ Why This Is an Access Control Issue**

This is an **access control vulnerability** because:

1. The server improperly authorizes users based solely on the `id` parameter in the URL, rather than validating the session cookie or user permissions.
2. There is no check to ensure that the requesting user is authorized to view the data associated with the `id` parameter.
3. Proper access control requires that sensitive operations be tied to authenticated sessions and enforce strict permissions on user actions.

---