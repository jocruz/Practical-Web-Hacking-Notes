
###  **Lab URL**:

[https://portswigger.net/web-security/file-path-traversal/lab-simple](https://portswigger.net/web-security/file-path-traversal/lab-simple)

---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit a **_Path Traversal vulnerability_** to retrieve the contents of the `/etc/passwd` file and complete the lab.
    
2. **What Makes This Vulnerable?**:
    
    - The application fails to validate user input in the `filename` parameter, enabling attackers to manipulate the file path.
    - This allows unauthorized access to sensitive server files using directory traversal sequences.
3. **Key Techniques**:
    
    - Using directory traversal sequences (e.g., `../../../`) to navigate the server's file system.
    - Leveraging **Burp Suite** to intercept and modify HTTP requests.
4. **Key Learning**:
    
    - Path Traversal vulnerabilities can expose sensitive files if input validation is inadequate.
    - Tools like **Burp Suite** and predefined wordlists enhance testing efficiency.

---

## **PortSwigger Solution**

1. **Intercept Request**:
    
    - Use **Burp Suite** to intercept the HTTP request that fetches a product image.
2. **Modify Payload**:
    
    - Replace the `filename` parameter with the following payload:
        
        ```plaintext
        ../../../etc/passwd
        ```
        
3. **Verify Response**:
    
    - Observe that the server returns the contents of the `/etc/passwd` file, confirming the exploit.

---

## **Your Solution**

1. **Traffic Generation**:
    
    - Navigate to the lab and interact with the web application to generate traffic.
        
    - Identify a GET request similar to:
        
        ```plaintext
        GET /images?filename=67.png
        ```
        
2. **Payload Construction**:
    
    - Replace `67.png` in the `filename` parameter with:
        
        ```plaintext
        ../../../etc/passwd
        ```

3. **Result**:
    
    - Review the server's response and verify that the `/etc/passwd` file contents are displayed.

4. **Completion**:
    
    - The lab is successfully completed by retrieving the contents of `/etc/passwd`.

---

## **Instructor Solution**

1. **Identify Wordlist**:
    
    - Use the following command in the terminal to locate LFI-related payload lists:
    
        ```bash
        find /usr/share/seclists -iname "*lfi*"
        ```
        
    - Relevant file located:
        
        ```plaintext
        /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
        ```
        
2. **Generate Traffic**:
    
    - Interact with the application and click **View Details** on product images to generate traffic in **Burp Suite**.
    - Adjust settings in Burp to ensure all MIME types (e.g., images, CSS, HTML) are captured.
3. **Test LFI Payloads**:
    
    - Intercept the request and set a positional marker on `67.png`.
        
    - Load the payload list from:
    
        ```plaintext
        /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
        ```
        
4. **Verify Responses**:
    
    - Use the **Render** tab in Burp Suite to visually confirm successful payloads that reveal the `/etc/passwd` file.
5. **Completion**:
    
    - The lab is completed when the `/etc/passwd` file is successfully retrieved.

---

## **Why This Solution is an Example of Path Traversal**

- **What is Path Traversal?**
    
    - A vulnerability where unvalidated user input allows attackers to navigate the server’s file system using directory traversal sequences.
- **How is this Lab Vulnerable?**
    
    - The `filename` parameter is used directly in the file path without validation, enabling exploitation.
- **Key Characteristics in This Lab**:
    
    - **_Target_**: The `filename` parameter in the GET request.
    - **_Exploitation_**: Path traversal sequences to access sensitive files.
    - **_Impact_**: Retrieval of sensitive system files like `/etc/passwd`.

---

#PathTraversal #BurpSuite #WebSecurity #FileInclusion