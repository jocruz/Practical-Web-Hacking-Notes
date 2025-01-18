
---

## **💡 Quick Overview**

This lab demonstrates an **SSRF vulnerability** in the stock check feature, where a user-supplied `stockApi` parameter allows unauthorized access to internal resources. By modifying the parameter to target `http://localhost/admin` and then sending a request to `http://localhost/admin/delete?username=carlos`, the attacker exploits SSRF to delete the user Carlos without authentication.

---

## **🛠️ Steps to Complete the Lab**

1. **Intercept the Stock Check Request**:
    
    - Locate the **POST /product/stock** request in Burp Suite.
    - Note the `stockApi` parameter.
2. **Verify SSRF with Collaborator**:
    
    - Replace the `stockApi` value with your Burp Collaborator URL.
    - Send the request and confirm interaction in the Collaborator tab.
3. **Access the Admin Interface**:
    
    - Modify `stockApi` to `http://localhost/admin` and send the request.
    - Observe the admin panel in the response.
4. **Delete Carlos**:
    
    - Modify `stockApi` to `http://localhost/admin/delete?username=carlos`.
	    - You can find this endpoint by looking at the raw or html code in the response in burp suite
    - Send the request and observe the **HTTP/2 302 Found** response.
5. **Complete the Lab**:
    
    - Confirm Carlos has been deleted in the lab interface.

---

## **❓ Why This is an SSRF Vulnerability**

1. **User-Controlled Input**:
    
    - The `stockApi` parameter allows users to specify a URL, which the server processes without validation.
2. **Access to Internal Systems**:
    
    - By targeting `http://localhost/admin`, attackers access an admin interface meant to be internal.
3. **Privilege Escalation**:
    
    - SSRF enables attackers to interact with privileged endpoints (e.g., `/admin/delete`) and perform administrative actions without proper authentication.
4. **Impact**:
    
    - Unauthorized access to sensitive internal resources and potential exploitation for further attacks.

---
