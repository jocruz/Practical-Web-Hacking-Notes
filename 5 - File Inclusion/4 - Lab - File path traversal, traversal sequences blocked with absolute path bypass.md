### 🌐 **Lab URL**:

[PortSwigger Lab: File Path Traversal - Absolute Path Bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

---

## 📋 **High-Level Summary of the Lab**

1. 🎯 **Objective**: Exploit a **Path Traversal vulnerability** to retrieve the contents of the `/etc/passwd` file and complete the lab.
    
2. 🛠️ **What Makes This Vulnerable?**:
    
    - The application fails to validate user input in the `filename` parameter, enabling attackers to manipulate the file path.
        
    - This allows unauthorized access to sensitive server files using directory traversal sequences.
        
3. 🔑 **Key Techniques**:
    
    - Using absolute paths (e.g., `/etc/passwd`) to navigate the server's file system.
        
    - Leveraging **Burp Suite** and **FFUF** to intercept and modify HTTP requests.
        
4. 📖 **Key Learning**:
    
    - Path Traversal vulnerabilities can expose sensitive files if input validation is inadequate.
        
    - Tools like **Burp Suite** and wordlists improve efficiency in finding LFI vulnerabilities.
        

---

## 🛡️ **PortSwigger Solution**

1. 🕵️‍♂️ **Intercept Request**:
    
    - Use **Burp Suite** to intercept the HTTP request that fetches a product image.
        
2. ✏️ **Modify Payload**:
    
    - Replace the `filename` parameter with the following payload:
        
        ```
        /etc/passwd
        ```
        
3. ✅ **Verify Response**:
    
    - Observe that the server returns the contents of the `/etc/passwd` file, confirming the exploit.
        

---

## 🎓 **Instructor Solution**

1. 🌐 **Traffic Generation**:
    
    - Navigate to the lab and interact with the web application to generate traffic.
        
    - Identify a GET request similar to:
        
        ```
        GET /product?filename=7.jpeg
        ```
        
2. 🧪 **Payload Construction with FFUF**:
    
    - Use **FFUF** to test for file inclusion paths:
        
        ```
        ffuf -u http://target.com/product?filename=FUZZ \
        -w /usr/share/seclists/SecLists-master/Fuzzing/LFI-jhaddix.txt \
        -mc 200,301,302,307,401,500
        ```
        
3. 📊 **Result**:
    
    - Review the server's response and verify that the `/etc/passwd` file contents are displayed.
        
4. 🏁 **Completion**:
    
    - The lab is successfully completed by retrieving the contents of `/etc/passwd`.
        

---

## ❓ **Why This Solution is an Example of Path Traversal**

- 🧐 **What is Path Traversal?**
    
    - A vulnerability where unvalidated user input allows attackers to navigate the server’s file system using directory traversal sequences.
        
- ⚠️ **How is this Lab Vulnerable?**
    
    - The `filename` parameter is used directly in the file path without validation, enabling exploitation.
        
- 🗝️ **Key Characteristics in This Lab**:
    
    - **_🎯 Target_**: The `filename` parameter in the GET request.
        
    - **_🛠️ Exploitation_**: Absolute path traversal to access sensitive files.
        
    - **_⚡ Impact_**: Retrieval of sensitive system files like `/etc/passwd`.
        

---

#️⃣ #PathTraversal #BurpSuite #Lab #FileInclusion