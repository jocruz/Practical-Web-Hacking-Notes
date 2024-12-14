
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a **_Server-Side Request Forgery (SSRF)_** vulnerability in the stock check feature to scan an internal IP range (`192.168.0.X`) for an admin interface on port `8080`, then use it to delete the target user, Carlos.
    
2. **What Makes This Vulnerable?**:
    
    - The application allows user-supplied URLs in the `stockApi` parameter without proper validation.
    - This enables the attacker to use the server to interact with internal resources, bypassing network restrictions.
3. **Steps**:
    
    - Use the `stockApi` parameter to scan for an internal admin interface.
    - Identify the correct IP address hosting the admin panel.
    - Send a crafted request to delete Carlos via the admin panel.
4. **Techniques Used**:
    
    - **_Payload enumeration_** via Burp Intruder and Turbo Intruder.
    - **_URL manipulation_** to exploit SSRF.
    - Writing and modifying a Python script to automate requests efficiently.
5. **Key Learning**:
    
    - SSRF vulnerabilities are often exploitable through parameterized URLs that accept user input.
    - Turbo Intruder is a powerful extension for **_high-speed enumeration_**, bypassing Burp Suite Community’s throttling limitations.

---

## **What to Do Next (Exam Simulation)**

- Analyze application functionality for user-controllable URL parameters.
- Use tools like **_Intruder, Turbo Intruder, or Collaborator_** to automate payload testing.
- Target internal IP ranges, ports, or endpoints likely to contain sensitive resources like admin panels.

---

## **Troubleshooting**

- **_If no admin panel is found_**:
    - Ensure the payload range (`1-255`) covers the entire IP range.
    - Verify URL encoding of the `stockApi` parameter.
- **_If deletion of Carlos fails_**:
    - Confirm the exact URL for deletion (`/admin/delete?username=carlos`).
    - Ensure the `stockApi` parameter is properly updated.

---

## **Key Takeaways**

- SSRF vulnerabilities allow attackers to **_interact with internal systems_** via a vulnerable server.
- Automating payload generation with tools like **_Turbo Intruder_** improves efficiency, especially on large IP ranges.
- Careful analysis of responses (e.g., HTTP status codes) helps identify the target resource.

---

## **Why This Solution is an Example of SSRF**

- **What is SSRF?**
    - Server-Side Request Forgery (SSRF) allows attackers to trick a server into making requests to internal or external systems on their behalf.
- **How is this Lab SSRF?**
    - The `stockApi` parameter enables the attacker to direct the server to make requests to internal IP addresses (`192.168.0.X`) and port `8080`.
    - By crafting requests, the attacker gains unauthorized access to the internal admin panel and performs privileged actions, such as deleting a user.
- **Key Characteristics in This Lab**:
    - **_Target_**: Internal admin interface at `192.168.0.X:8080`.
    - **_Exploitation_**: Modify the `stockApi` parameter to scan for the admin panel and interact with it.
    - **_Impact_**: Unauthorized resource access and privilege escalation.

---

## **Step 1: Explore the Web Application**

1. Navigate to the "View details" button and interact with the stock check dropdown.
2. Use Burp Suite to intercept stock check requests. Look for **POST /product/stock** requests.

---

## **Step 2: Investigate and Modify the Request**

1. Send the intercepted request to **Burp Repeater** (Ctrl+R).
2. Decode the `stockApi` parameter to inspect the full URL:
    
    ```plaintext
    stockApi=http://192.168.0.1:8080/product/stock/check?productId=1&storeId=1
    ```
    
3. Modify the `stockApi` parameter to target the admin panel:
    
    ```plaintext
    stockApi=http://192.168.0.1:8080/admin
    ```
    
4. Send the modified request to **Burp Intruder** to automate the attack.

---

## **Step 3: Automate Payloads with Burp Intruder**

1. Highlight the final octet of the IP address (e.g., `1`) and mark it as a payload position.
2. In the Payloads tab:
    - Set Payload Type to **Numbers**.
    - Configure the range:
        - From: `1`
        - To: `255`
        - Step: `1`.
3. Start the attack. Note that Burp Community is throttled, making this process slow.

---

## **Step 4: Speed Up with Turbo Intruder**

1. Install the **Turbo Intruder** extension.
    
2. Return to Burp Repeater and launch Turbo Intruder from the Extensions menu.
    
3. Replace the default for loop with the following Python code:
    
    ```python
    for i in range(1, 256):
        stockApi = 'http://192.168.0.%s:8080/admin' % i
        engine.queue(target.req, stockApi)
    ```
    
    - **Explanation**:
        - The `for` loop iterates through the range `1-255`.
        - The variable `stockApi` dynamically replaces `%s` with the current value of `i`.
        - Each iteration sends a modified request to the `stockApi` parameter, targeting `192.168.0.X:8080/admin`.
4. Modify the request argument in Turbo Intruder to:
    
    ```plaintext
    stockApi=%s
    ```
    
5. Start the attack and filter responses by HTTP status code.
    

---

## **Step 5: Identify the Admin Panel**

1. Look for a **200 status code** in the results. Note the matching IP address (e.g., `192.168.0.236`).
2. Verify the admin panel by sending the request to **Turbo Intruder Render**.

---

## **Step 6: Delete Carlos**

1. In the Render tab of Turbo Intruder, locate the `<a>` tag with the delete user URL:
    
    ```html
    <a href='http://192.168.0.236:8080/admin/delete?username=carlos'>
    ```
    
2. Modify the `stockApi` parameter in Repeater:
    
    ```plaintext
    stockApi=http://192.168.0.236:8080/admin/delete?username=carlos
    ```
    
3. Send the request and view the Render tab to confirm the lab completion.

---

### **Screenshot of Turbo Intruder Results**

![[Pasted image 20241214020309.png]]

---

#SSRF #turbointruder #lab  #python #serverinteraction