# 🚀 **_Lab: File Path Traversal, Validation of Start of Path_**

## 🌐 Lab URL:

[PortSwigger Lab: File Path Traversal, Validation of Start of Path](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)

---

- **_Objective:_** Retrieve the contents of the `/etc/passwd` file.
    
- **_Vulnerability Cause:_** **_Path traversal vulnerability_** due to insufficient validation of the start of the path parameter.
    
- **_Key Techniques:_**
    
    - **_Burp Suite_** for intercepting and modifying HTTP requests.
        
    - **_Path manipulation_** using directory traversal techniques.
        
- **_Key Learnings:_**
    
    - **_Manual testing_** can be more effective than automated fuzzing in certain scenarios.
        
    - **_Understanding request structure_** and path validation logic.
        

---

## 📖 PortSwigger Solution:

1. Use **_Burp Suite_** to intercept and modify a request fetching a product image.
    
2. Modify the `filename` parameter as follows:
    

```
/var/www/images/../../../etc/passwd
```

3. Observe the server's response, which reveals the contents of the `/etc/passwd` file.
    

---

## 🧩 Your Solution:

1. Launch **_Burp Suite_** and enable intercept mode.
    
2. Capture a request for a product image (`**_/image?filename=/var/www/images/5.png_**`).
    
3. Send the request to **_Repeater_**.
    
4. Attempt to modify the `filename` parameter and test for path traversal manually:
    
    - **_Removing_** `/var/www/images/` returns a `Missing Filename` error.
        
    - **_Restoring_** `/var/www/` (without `images`) produces the same error.
        
5. Modify the payload to attempt traversal:
    

```
/image?filename=/var/www/images/../../../etc/passwd
```

6. Observe the successful response containing the `/etc/passwd` file contents.


---

## 🎓 Instructor Solution:

1. **_Load the lab_** and generate traffic.
    
2. Enable **_Burp Suite_** filtering settings:
    
    - Go to **_Filter by MIME type_**.
        
    - Uncheck all options (**_CSS, images, and binary files_**).
        
3. Return to **_Burp Proxy_** and locate the image request:
    

```
/image?filename=/var/www/images/5.png
```

4. **_Send the request_** to **_Repeater_**.
    
5. Attempt **_manual testing_** by modifying the path:
    
    - **_Removing_** `/var/www/images/` results in a `Missing Filename` error.
        
    - **_Including_** `/var/www/` without `images` gives the same error.
        
6. Use the payload:
    

```
/image?filename=/var/www/images/../../../etc/passwd
```

7. The server responds with the contents of `/etc/passwd`.
    

**_Key Insight:_** Automated fuzzers may miss this vulnerability; **_manual testing_** is crucial.

---
# 📖 Why Does `**../../**`  Work?

- The `../` sequence is used in file paths to move up one directory level.
    
- When you use `/var/www/images/../../../etc/passwd`, it works as:
    
    - `/var/www/images/` (the starting path)
        
    - The first `../` moves up from `images` to `www`.
        
    - The second `../` moves up from `www` to `var`.
        
    - The third `../` moves up from `var` to the root directory `/`.
        
- After moving up, `/etc/passwd` becomes accessible if directory traversal protections are weak.
    

### Can I Do This in My Terminal?

- Yes, you can try it in your terminal using:
    
    ```
    cd Documents/Notes/../../
    ```
    
- This will take you back to your home directory because it moves up two levels from `Notes` to `Documents` and then back to the home directory.
    
- This works due to how the file system resolves paths in Unix-based systems.


## 🔍 Why This Solution is an Example of Path Traversal:

- **_Target:_** The web application's **_image retrieval endpoint_**.

- **_Exploitation:_** Manipulating the `filename` parameter to **_traverse directories_** beyond the expected folder.

- **_Impact:_** Unauthorized disclosure of **_sensitive system files_** (`/etc/passwd`).

---

## 🏷️ **_Hashtags and Tags:_**

#FileInclusion #Lab #PathTraversal #LFI #PortSwigger