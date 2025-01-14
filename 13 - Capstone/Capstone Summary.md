### 📚 Capstone Web Application Penetration Testing Walkthrough

---

### 🚀 **High-Level Summary**

This **Capstone Walkthrough** provides a comprehensive methodology for **web application penetration testing** using a local web application. The focus is on systematically identifying and exploiting security vulnerabilities, including:

- **IDOR (Insecure Direct Object References)**
    
- **JWT Manipulation and Forgery**
    
- **XSS (Cross-Site Scripting)**
    
- **Race Conditions**
    

The instructor emphasizes the importance of **careful observation**, **documenting each step**, and **testing all inputs and outputs**.

---

### 📦 **Definition Bank**

|**Term**|**Definition**|
|---|---|
|**IDOR (BOLA)**|Insecure Direct Object Reference (Broken Object Level Authorization) allows access to unauthorized data by manipulating IDs.|
|**JWT (JSON Web Token)**|A token used for **authentication** and **session management** with a signed payload.|
|**XSS (Cross-Site Scripting)**|Injecting **malicious scripts** into web pages, often leading to data theft or account compromise.|
|**Race Condition**|A flaw where multiple actions happen at once, causing **unexpected behavior** like double payments.|
|**BLAF/BLAC**|Broken **Level of Access Control**, bypassing user role restrictions.|

---

### 📌 **Key Attacks and Testing Methodologies**

#### ✅ **1. IDOR (Insecure Direct Object Reference)**

- Attempt to **access restricted data** by modifying object IDs (e.g., `/purchase-history?id=1`).
    
- **Methodology:**
    
    - Identify numerical **ID parameters** in URLs.
        
    - Test by incrementing or decrementing IDs in **Burp Suite Repeater**.
        
    - Confirm successful exploitation when accessing another user's data.
        

---

#### ✅ **2. JWT (JSON Web Tokens) Manipulation**

- JWTs are used for **authentication** and can be manipulated if improperly implemented.
    
- **Methodology:**
    
    - **Check if JWT is used:** Remove the token and resend the request.
        
    - **Test for Weak Secrets:** Crack using Kali tool:
        
        ```
        python3 jwt_tool.py [token] --crack -d /usr/share/seclists/passwords.txt
        ```
        
    - **Forge JWTs:** Modify the token on `jwt.io` by changing the **ID** and signing with a cracked key.
        

---

#### ✅ **3. XSS (Cross-Site Scripting)**

- Attempt to execute **JavaScript payloads** by reflecting unsanitized input.
    
- **Methodology:**
    
    - Identify **reflected parameters** (e.g., `/login?error=Testing`).
        
    - Test simple payloads like:
        
        ```
        <script>alert('XSS')</script>
        ```
        
    - If filtered, use **tag variation tests** (e.g., `<body onkeypress=prompt()>`).
        
    - Automate testing in **Burp Intruder** using **Cluster Bomb** with tag and event handler combinations.
        

---

#### ✅ **4. Race Conditions**

- Exploit simultaneous requests to **manipulate data processing.**
    
- **Methodology:**
    
    - Identify critical actions like **checkout** and **basket update**.
        
    - Send multiple requests in **Burp Suite Repeater**:
        
        - `/checkout` (once)
            
        - `/shop/basket/update` (multiple times)
            
    - Use **Send Group in Parallel** with **last-byte synchronization**.
        

---

### 📡 **Step-by-Step Web App Pen Testing Walkthrough**

#### **Step 1: Initial Web App Exploration**

- Open the web app (`localhost`).
    
- **View Source:** (`Ctrl+U`) to inspect for any scripts or unusual elements.
    
- **Example:** A `/js/custom.js` file was found but not accessible.
    

#### **Step 2: Account Creation and Testing Access Controls**

- Create **two accounts**:
    
    - **Poppy:** `poppy@test.com`, `poppy`
        
    - **Elfie:** `elfie@test.com`, `elfie`
        
- **Verify only one account** and keep the other unverified to test **access control**.
    

#### **Step 3: JWT Identification and Handling**

- Identify JWT tokens in **Burp Suite HTTP traffic**.
    
- Verify if JWT is required for each endpoint by **removing the token**.
    

#### **Step 4: IDOR Testing**

- Go to `/purchase-history?id=1`.
    
- Modify the ID and send the request for **unauthorized data access**.
    
- Verify if JWT is needed by **removing the token** and testing access.
    

#### **Step 5: JWT Manipulation and Cracking**

- Crack the **JWT secret key** using Kali:
    

```
python3 jwt_tool.py [token] --crack -d /usr/share/seclists/passwords.txt
```

- Modify the **user ID** and **token type** using `jwt.io`.
    
- Forge a token and resend it using **Burp Suite Repeater**.
    

#### **Step 6: XSS Testing**

- Identify **reflected inputs** (`/basket?success=Hello`).
    
- Test payloads with **varied tags and event handlers**.
    
- **Cluster Bomb Test:** Use **Burp Intruder** to automate event handler testing.
    

#### **Step 7: Race Condition Testing**

- Add items to the **basket**.
    
- Send multiple requests for `/shop/basket/update` while **simultaneously checking out**.
    
- Confirm by observing **quantity changes** and **incorrect totals**.
    

---

### 🧰 **Tools Used in the Capstone Walkthrough**

- **Burp Suite:** Repeater, Intruder, JWT Editor Extension
    
- **Kali Linux:** `jwt_tool.py`
    
- **jwt.io:** Token Manipulation & Decoding
    
- **Python Scripts:** For manual ID bruteforcing
    

---

### ✅ **Conclusion and Key Takeaways**

- **Document Every Step:** Keep track of endpoints and traffic history.
    
- **Test JWT Requirements:** Always verify if JWT is essential for the endpoint before testing token manipulations.
    
- **Test for Broken Access Control:** Use **IDOR attacks** and **unverified users** for testing.
    
- **Automate When Possible:** Tools like **Burp Intruder** and **jwt_tool** help automate common attacks.
    
- **Critical Thinking:** Always **validate your results** (e.g., confirming XSS execution or IDOR success).
    

**Final Tip:** 💡 _Always stay organized and follow a_ **_structured methodology_** _for the open book exam._