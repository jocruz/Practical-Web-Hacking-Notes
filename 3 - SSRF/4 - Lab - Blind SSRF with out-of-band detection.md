
---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a **_Blind Server-Side Request Forgery (SSRF)_** vulnerability to make the server send HTTP requests to the Burp Collaborator server and verify this through out-of-band (OAST) detection.
    
2. **What Makes This Vulnerable?**:
    
    - The application uses a **Referer header** to fetch analytics data, but it does not validate the input URL.
    - This allows an attacker to manipulate the Referer header to trigger HTTP requests to external or internal servers.
      
3. **Steps**:
    
    - Modify the Referer header in the product request to include a Burp Collaborator payload.
    - Verify the interaction with the Collaborator server to confirm Blind SSRF.
      
4. **Techniques Used**:
    
    - **_Referer header manipulation_** to inject a malicious URL.
    - Using **_Burp Collaborator_** to verify server-side requests.
      
5. **Key Learning**:
    
    - **_Blind SSRF_** occurs when the server makes requests to attacker-controlled resources without providing a direct response in the application.
    - Out-of-band detection tools like Burp Collaborator are crucial for detecting and verifying Blind SSRF vulnerabilities.

---

## **What to Do Next (Exam Simulation)**

- Investigate **_headers, body parameters, and query strings_** for possible SSRF injection points.
- Use tools like **_Burp Collaborator_** or alternatives like `webhook.site` to confirm traffic generated by Blind SSRF.

---

## **Troubleshooting**

- **_If no traffic is observed in Collaborator_**:
    
    - Wait a few seconds and poll Collaborator again, as the request may be delayed.
    - Ensure the payload is correctly formatted and includes the full Collaborator URL with `http://`.
      
- **_If using webhook.site_**:
    
    - Ensure the unique URL is correct and visible on the webhook.site dashboard.

---

## **Key Takeaways**

- Blind SSRF allows attackers to make unauthorized requests without directly observing responses in the application.
- Tools like **_Burp Collaborator_** simplify the process of detecting Blind SSRF through out-of-band traffic.
- Even without Burp Suite Pro, tools like `webhook.site` can prove the vulnerability exists by verifying external interactions.

---

## **Why This Solution is an Example of SSRF**

- **What is Blind SSRF?**
    - Blind SSRF occurs when the server processes user-supplied URLs and sends requests without exposing the responses to the attacker.
- **How is this Lab SSRF?**
    - The Referer header accepts external URLs and triggers server-side HTTP requests.
    - By inserting a Collaborator payload, the attacker can confirm the SSRF through out-of-band interactions recorded by Collaborator.
- **Key Characteristics in This Lab**:
    - **_Target_**: The server-side analytics software processes the Referer header.
    - **_Exploitation_**: Manipulate the Referer header to point to an external server (e.g., Collaborator).
    - **_Impact_**: Proves the ability to trigger server-side requests, potentially allowing further attacks like internal network access.

---

## **Step 1: Explore the Web Application**

1. Navigate to the lab and select a product to view details.
    - Example URL: `/product?productId=1`.
2. Intercept the GET request in **Burp Suite** with the Referer header:
    
    ```plaintext
    Referer: https://0a4e0069004affadb82385b6d003f009.web-security-academy.net/
    ```
    
3. Send the request to **Repeater** (Ctrl+R).

---

## **Step 2: Test the Referer Header**

1. Modify the Referer header in Repeater to another URL:
    
    ```plaintext
    Referer: http://example.com/
    ```
    
2. Send the request. If the server responds with **HTTP 200**, it indicates the Referer header is not validated.

---

## **Step 3: Insert Collaborator Payload**

1. In the Referer header, replace the original domain with a **Burp Collaborator payload**:
    
    ```plaintext
    Referer: http://<unique-collaborator-url>
    ```
    
2. Use the **"Insert Collaborator Payload"** option in Burp Suite to generate and insert the payload.
3. Send the request.

---

## **Step 4: Verify Blind SSRF**

1. Go to the **Collaborator** tab in Burp Suite and click **Poll now**.
2. Observe DNS and HTTP interactions from the application to the Collaborator server.
    - Example:
        
        ```plaintext
        Interaction type: DNS
        Hostname: <unique-collaborator-url>
        Interaction type: HTTP
        HTTP request to <unique-collaborator-url>
        ```
        
3. If you see interactions, the Blind SSRF vulnerability is confirmed.

---

## **Alternative Approach Without Burp Suite Pro**

1. Use **webhook.site** to generate a unique URL.
2. Replace the Referer header with the webhook.site URL:
    
    ```plaintext
    Referer: http://<unique-webhook-url>
    ```
    
3. Verify traffic on the webhook.site dashboard.
4. This approach can confirm Blind SSRF even without Burp Suite Pro.

---

## **Instructor Notes**

- Blind SSRF doesn’t return visible data in the application response, making tools like Collaborator or webhook.site essential for verification.
- Reporting SSRF vulnerabilities often requires demonstrating potential impact, such as internal network access or sensitive data exfiltration.
- In a bug bounty scenario, ensure the vulnerability is in scope before proceeding.

---

#SSRF #blindssrf #burpsuitepro #collaborator #refererheader #accesscontrol