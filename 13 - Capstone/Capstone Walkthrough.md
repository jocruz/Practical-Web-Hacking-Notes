
---

# Capstone Web Application Penetration Testing Walkthrough

---

## 🎯 Objective:

This walkthrough demonstrates the methodology for a complete web application penetration test, covering account enumeration, IDOR, JWT attacks, XSS, and race conditions. This process serves as a blueprint for approaching web app pen testing in an open book exam.

---

## 🖥️ Initial Recon: Exploring the Web Application

1. **Start at the root of the web application (`localhost`).**
2. **View the source code:** Press `Ctrl + U` to inspect the source.
3. **Identify references:** A reference to `<script src="/js/custom.js">` was found but led to a `"CANNOT GET /js/custom.js"` error.

---

## 📌 Exploring the Attractions Page:

1. Navigate to the **Attractions tab.**
2. Encounter the error URL:
    
    ```plaintext
    localhost/login?error=You%20need%20to%20login%20before%20accessing%20this%20page
    ```
    
3. **Error Message Reflection Testing:**
    - Modified the error parameter: `localhost/login?error=Testing`
    - The modified message was reflected, suggesting **XSS/Template Injection** potential.

---

## 🧑‍💻 User Registration and Verification:

1. **Create two test accounts:**
    
    - **Poppy:** `poppy:poppy`, `poppy@test.com`
    - **Elfie:** `elfie:elfie`, `elfie@test.com`
2. **Verification:**
    
    - **Poppy's Verification Link:** `http://localhost/verify/2`
    - **Key Vulnerability:** The `id=2` parameter is guessable and could be brute-forced using:
        - **Burp Suite Sniper** with a range of `1-100`.
        - **Python curl-based script** to send POST requests.
3. **Proceed with Verification:** Verify **Poppy's** account and log in.
    

---

## 🛒 Web Application Flow Exploration:

1. **Explore:** `/attractions`, `/shop`, `/basket`.
    
2. **Add Item to Basket:**
    
    - Added a **JimaJura baseball cap** to the basket.
3. **Traffic Analysis in Burp Suite:**
    
    - **POST /shop/basket/add** with payload:
        
        ```plaintext
        itemid=1&quantity=1
        ```
        
4. **Checkout:**
    
    - **/checkout** endpoint returned success:
        
        ```plaintext
        localhost?/success=Payment%20successful
        ```
        

✅ **Observation:** Potential for **payment injection and race conditions.**

---

## 🛡️ IDOR (Insecure Direct Object Reference):

1. **Explore `/basket` and `/purchase-history`:**
    
    - Observed a call to **/purchase-history?id=2**.
2. **Test for IDOR in Burp Suite:**
    
    - Change `GET /purchase-history?id=2` → `GET /purchase-history?id=1`.
3. **Create Unique Purchase Histories for Testing:**
    
    - Add more unique items for **Elfie** and repeat the test by switching back to **Poppy**.
4. **Result:**
    
    - Data from **Elfie’s** account was retrieved in **Poppy's** session.

✅ **Confirmed IDOR (Broken Object Level Access).**

---

## 🔑 JWT Testing:

### ✅ **Token Usage Check:**

- Removed `Cookie: token` in `/basket` and received a **302 redirect** (enforcing JWT).
- Removed the token in `/purchase-history`, but data was still retrieved, indicating **JWT was not enforced**.

---

### ✅ **JWT Cracking Attempt:**

- Used `jwt_tool` to crack the token:
    
    ```bash
    python3 jwt_tool.py [JWT_TOKEN] --crack -d /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt
    ```
    
    - **Key Found:** `secret`

---

### ✅ **Token Forgery:**

- Used `jwt.io` to modify:
    - Changed `sub: 1` → `sub: 2` (Poppy to Elfie)
    - Changed algorithm to `"secret"` and signed with the cracked key.

---

### ✅ **Replay Attack:**

- Replaced **Poppy's** token with the modified **Elfie's** token in Burp Suite.
- **Outcome:** Successfully accessed **Elfie's** purchase history.

✅ **Confirmed: Token Forgery Successful.**

---

## 💥 XSS Testing:

1. **Test Reflected XSS:**
    - **Target:** `/basket?success=`
    - Payload:
        
        ```plaintext
        localhost/basket?success=<img src=x onerror=prompt()>
        ```
        
    - **Filtered.**

---

2. **Automating with Burp Suite Intruder (Cluster Bomb Attack):**
    
    - **Payload Set 1:** Various HTML tags like `<body>`.
    - **Payload Set 2:** Event handlers like `onkeypress`.
    - **Grep Match:** Added `"filtered"` to detect bypass attempts.
3. **Successful Payload Discovered:**
    
    ```plaintext
    localhost/basket?success=<body onkeypress=prompt()>
    ```
    

✅ **XSS Triggered on Key Press.**

---

## 🏁 Race Condition Testing:

1. **Target Endpoints Identified:**
    
    - **/checkout** (payment endpoint)
    - **/shop/basket/update** (adding items to the basket)
2. **Setup in Burp Repeater:**
    
    - Send `/checkout` once.
    - Send `/shop/basket/update` (10 times).
    - Grouped together for **Parallel Requests (Last-Byte Sync)**.
3. **Execution Steps:**
    
    - Ensure an item is in the basket.
    - Send the group in parallel.
4. **Observation:**
    
    - Basket showed **16 baseball caps** instead of 1.
    - Price changed from **19.99** to **319.84**.

✅ **Race Condition Successfully Confirmed.**

---

## 🎯 **Key Takeaways:**

- ✅ **Account Enumeration:** Always create **two accounts** for web app testing.
- ✅ **JWT Handling:** Confirm if a JWT is enforced **before** attacking the token.
- ✅ **IDOR Testing:** Test **purchase history** and **basket** data for Broken Object Level Access.
- ✅ **XSS Testing:** Use **Intruder** for **tag fuzzing** and **event handler** discovery.
- ✅ **Race Condition Testing:** Group parallel requests in **Burp Suite** for **race window exploitation**.

✅ **Walkthrough Completed Successfully. This methodology ensures thorough web application penetration testing coverage!** 🚀