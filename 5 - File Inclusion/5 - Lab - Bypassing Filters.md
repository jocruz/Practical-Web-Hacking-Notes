### 🌐 **Lab URL**:

Guided lab from Lab Files

Payload all the things url:
https://swisskyrepo.github.io/PayloadsAllTheThings/File%20Inclusion/Wrappers/#wrapper-phpfilter [for whatever reason this one works best]

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion


---

## 📋 **High-Level Summary of the Lab**

1. 🎯 **Objective**: Understand how filters and PHP wrappers work in file inclusion vulnerabilities.
    
2. 🛠️ **What Makes This Vulnerable?**:
    
    - Lack of proper input validation allows attackers to use PHP wrappers to retrieve sensitive data.
        
3. 🔑 **Key Techniques**:
    
    - Identifying allowed and blocked characters.
        
    - Testing recursive filters and encoded payloads.
        
    - Using PHP wrappers for data extraction and encoding.
        
4. 📖 **Key Learning**:
    
    - Proper validation of file inclusion paths is crucial to preventing sensitive data leakage.
        
    - PHP wrappers can be leveraged to encode sensitive data for easier exfiltration.
        

---

## 🛡️ **Instructor Solution**

1. 🌐 **Traffic Generation**:
    
    - Navigate to the application running on `localhost` and interact with language selection to generate traffic.
        
    - Identify a request similar to:
        
        ```
        localhost/?lang=language/en.php
        ```
        
2. 🧪 **Payload Construction with PHP Wrappers**:
    
    - Use the following payload in the `lang` parameter:
        
        ```
        localhost/?lang=php://filter/convert.base64-encode/resource=secret.php
        ```
        
3. 📊 **Result**:
    
    - Observe base64-encoded content in the server response.
        
    - Decode the Base64 data using CyberChef or a terminal command:
        
        - **CyberChef**:
            
            - Paste the data into CyberChef.
                
            - Apply the `From Base64` recipe.
            
        - **Terminal**:
            
            ```
            echo <base64-data> | base64 -d
            ```

4. 🏁 **Completion**:
    
    - Successfully retrieved sensitive data from the `secret.php` file.
        

---

## 🔎 **Why This Solution is an Example of File Inclusion Vulnerability**

- 🧐 **What is File Inclusion Vulnerability?**
    
    - A vulnerability where unsanitized input allows direct inclusion of files from the server.
        
- ⚠️ **How is this Lab Vulnerable?**
    
    - The application fails to restrict file paths and PHP wrappers, allowing unauthorized file access.
        
- 🗝️ **Key Characteristics in This Lab**:
    
    - **🎯 Target**: The `lang` parameter in the GET request.
        
    - **🛠️ Exploitation**: Usage of PHP wrappers for file inclusion and encoding.
        
    - **⚡ Impact**: Exposure of sensitive data such as hardcoded credentials.
        

---

#️⃣ #FileInclusion #PHPWrappers #Lab