
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a **_Server-Side Request Forgery (SSRF)_** vulnerability in the stock check feature to access the admin interface and delete the target user, Carlos.
    
2. **What Makes This Vulnerable?**:
    
    - The application allows user-supplied URLs in the `stockApi` parameter without proper validation or restriction.
    - This vulnerability enables attackers to send requests to internal systems (e.g., `localhost`) from the server, bypassing client-side restrictions.
3. **Steps**:
    
    - Intercept the stock check request and modify the `stockApi` parameter.
    - Use the SSRF to access the admin interface at `http://localhost/admin`.
    - Delete Carlos by sending the URL `http://localhost/admin/delete?username=carlos` through the vulnerable `stockApi` parameter.
4. **Techniques Used**:
    
    - **_URL manipulation_** to exploit the SSRF vulnerability.
    - Burp Suite Collaborator for external request verification.
    - Using **_URL encoding_** to ensure properly formatted requests.
5. **Key Learning**:
    
    - SSRF vulnerabilities can arise when applications allow user input to control server-side requests without validation.
    - External tools like Burp Suite Collaborator can verify SSRF exploits effectively.

---

## **What to Do Next (Exam Simulation)**

- Look for **_user-supplied parameters_** that accept full or partial URLs in API requests.
- Test requests using **_Collaborator or webhook.site_** to confirm server-side interactions.
- Try to access internal systems or admin functionality by manipulating the vulnerable parameter.

---

## **Troubleshooting**

- **_If SSRF is not working_**:
    - Verify the URL encoding for the `stockApi` parameter.
    - Check if the server is blocking specific hosts or ports (e.g., `localhost:80`).
- **_If the admin interface doesn’t load_**:
    - Confirm that the request is correctly targeting `http://localhost/admin`.
    - Ensure there are no typos or encoding issues in the modified URL.

---

## **Key Takeaways**

- SSRF vulnerabilities allow attackers to make **_arbitrary requests on behalf of the server_**.
- URL encoding is critical to ensure the server interprets the request correctly.
- Burp Suite Collaborator is a powerful tool for verifying server-side interactions initiated via SSRF.

---

## **Step 1: Initial Application Exploration**

1. Step through the application and its functionality to understand how it works.
    - For example, test **My Account**, **Authentication and Authorization**, and **Products** separately.
2. Navigate to the Products page and select different stores using the stock check dropdown.
3. Observe that stock updates occur without a page refresh, indicating an API-based interaction common in modern SPAs (single-page applications).

---

## **Step 2: Intercept the Stock Check Request**

1. In Burp Suite, locate the **POST /product/stock** requests in the HTTP history.
2. Note the `stockApi` parameter in the request body, which looks like:
    
    ```plaintext
    stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
    ```
    
3. Decode the URL using Burp Decoder:
    - Example decoded URL:
        
        ```plaintext
        http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
        ```
        

---

## **Step 3: Verify SSRF Using Collaborator**

1. Use **Burp Collaborator** or an external tool like `webhook.site` to verify SSRF.
2. Copy your **unique Collaborator URL** to the clipboard.
3. Modify the `stockApi` parameter to include your Collaborator URL:
    
    ```plaintext
    stockApi=http://<collaborator-unique-url>
    ```
    
4. Encode the modified URL in Burp Suite (Ctrl+U for URL encoding).
5. Send the request and check Collaborator for incoming requests.

---

## **Step 4: Access the Admin Interface**

1. Modify the `stockApi` parameter to target the admin panel:
    
    ```plaintext
    stockApi=http%3A%2F%2Flocalhost%2Fadmin
    ```
    
2. Send the request in Burp Repeater.
3. In the **Render** tab of the response, observe the admin interface, which allows user management.

---

## **Step 5: Identify the URL to Delete Carlos**

1. Inspect the admin interface’s HTML in the **Pretty** tab.
2. Locate the `<a>` tag containing the delete user URL:
    
    ```plaintext
    /admin/delete?username=carlos
    ```
    

---

## **Step 6: Submit the Delete Request**

1. Modify the `stockApi` parameter to include the delete URL:
    
    ```plaintext
    stockApi=http%3A%2F%2Flocalhost%2Fadmin%2Fdelete%3Fusername%3Dcarlos
    ```
    
2. Send the request in Burp Repeater.
3. Observe the response with **HTTP/2 302 Found**.
4. Verify in the lab interface that Carlos has been successfully deleted.

---

## **Step 7: Render the Completed Lab**

1. Return to the lab interface to confirm the lab completion message.
2. Alternatively, resend the request in Burp Repeater and view the **Render** tab for the completed lab message.

---

## **Why This Solution is an Example of SSRF**

**Server-Side Request Forgery (SSRF)** occurs when an attacker can manipulate a server-side application to make unauthorized requests to internal or external resources. This lab demonstrates SSRF because:

1. **User-Supplied Input Controls the Server's Requests**:
    
    - The `stockApi` parameter allows users to specify a URL.
    - The server blindly uses this URL to make requests, enabling attackers to control where the server sends requests.
2. **Access to Internal Resources**:
    
    - By modifying the `stockApi` parameter to target `http://localhost/admin`, the attacker uses the server to access an internal admin interface that is otherwise inaccessible from the client.
3. **Privilege Escalation**:
    
    - The attacker exploits the SSRF to interact with the admin panel and perform administrative actions (e.g., deleting a user) without being authenticated as an admin.
4. **Indicators of SSRF**:
    
    - The ability to inject external or internal URLs into the `stockApi` parameter.
    - Using a unique Burp Collaborator URL confirms that the server processes attacker-specified requests.

### **Core Characteristics of SSRF in This Lab**

- **Target**: Internal systems (`localhost`).
- **Exploitation**:
    - Modify `stockApi` to point to `http://localhost/admin` to access the admin interface.
    - Use SSRF to interact with the `/admin/delete` endpoint to delete Carlos.
- **Impact**: Unauthorized access to sensitive functionality and potential for further exploitation, such as stealing data or escalating privileges.

---

This lab highlights the dangers of SSRF, especially when:

- Applications do not validate user-supplied URLs.
- Sensitive internal systems are accessible from within the server's network.

---

#SSRF #accesscontrol #lab #URLencoding #serverinteraction