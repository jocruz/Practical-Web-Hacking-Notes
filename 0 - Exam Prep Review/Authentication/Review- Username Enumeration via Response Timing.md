
---

# **⏳ Username Enumeration via Response Timing**

## **💡 Quick Overview**

1. **Headers to Bypass Lockout**: Use headers like `X-Forwarded-For`, `X-Real-IP`, and others to manipulate requests and bypass lockout mechanisms. Add these headers with the same value:
    
    ```plaintext
    X-Real-IP: 1.2.3.4
    X-Forwarded-For: 1.2.3.4
    X-Originating-IP: 1.2.3.4
    Client-IP: 1.2.3.4
    True-Client-IP: 1.2.3.4
    ```
    
    Increment the IP values (e.g., `1.2.3.4` → `1.2.3.5`) to reset lockouts.
    
2. **Timing-Based Username Enumeration**: Use a **long password string** (e.g., `testtest...`) to analyze response times:
    
    - If the username is **valid**, the application hashes the password, causing a **longer response time**.
    - If the username is **invalid**, the application quickly rejects it, resulting in a **shorter response time**.
3. **Solution Workflow**:
    
    - Identify the critical header (`X-Forwarded-For`) that allows bypassing lockouts.
    - Configure **Burp Intruder** to:
        - Enumerate usernames by observing response times.
        - Fuzz passwords using a valid username and a password list.
    - Look for a **302 Found** response to confirm the correct username-password pair.
4. **Key Takeaway**: Response timing differences and user-controlled headers (e.g., `X-Forwarded-For`) can be exploited for enumeration, bypassing brute-force protections.
    

---

## **🛠️ Lab Goal**

- **Objective**: Exploit response timing and header manipulation to enumerate valid usernames and passwords for a login form, bypassing lockout mechanisms.

---

## **🚀 Solution Summary**

### **1️⃣ Header Manipulation**

- Use headers like `X-Forwarded-For` to bypass lockout mechanisms.
- Increment the IP in the header (e.g., `1.2.3.4` → `1.2.3.5`) to reset the lockout.

### **2️⃣ Timing-Based Username Enumeration**

- Set a long password string (e.g., `testtest...`) in the request.
- Analyze response times:
    - A valid username triggers a longer response due to password hashing.
    - Invalid usernames are rejected faster.

### **3️⃣ Password Cracking**

- Use the identified valid username with a password list in **Burp Intruder**.
- Identify the correct password by finding a **302 Found** response.

---

## **📌 Key Takeaways**

### **1️⃣ Response Timing Reveals Valid Usernames**

- Applications using hashing algorithms (e.g., bcrypt) spend more time processing valid usernames.
- Exploiting this timing difference allows enumeration.

### **2️⃣ IP Header Spoofing Bypasses Lockout**

- Headers like `X-Forwarded-For` allow resetting lockouts by spoofing IPs.
- Incrementing the last digit of the IP effectively resets the lockout.

### **3️⃣ Best Practices for Secure Applications**

- Ensure error responses for login attempts are consistent in timing and content.
- Rate-limit requests and restrict the use of user-controlled headers like `X-Forwarded-For`.

---

## **🔄 Steps to Complete the Lab**

### **1️⃣ Checklist of Headers**

Add these headers to requests:

```plaintext
X-Real-IP: 1.2.3.4
X-Forwarded-For: 1.2.3.4
X-Originating-IP: 1.2.3.4
Client-IP: 1.2.3.4
True-Client-IP: 1.2.3.4
```

### **2️⃣ Locate the Login Request**

- Attempt to log in to the application.
- Find the `POST /login` request in **HTTP history**.
- Send the request to **Repeater**.

### **3️⃣ Test Lockout Behavior**

- Add the headers from the checklist to the request.
- Trigger a lockout with multiple failed attempts.
- Modify the last digit of the IP in the headers to bypass the lockout (e.g., `1.2.3.4` → `1.2.3.5`).

### **4️⃣ Identify the Critical Header**

- Remove one header at a time to find the one responsible for unblocking.
- **Result**: The `X-Forwarded-For` header is the key bypass tool.

### **5️⃣ Configure Intruder for Username Enumeration**

- Send the modified request to **Intruder**.
- Place positional markers on:
    - The last digit of the `X-Forwarded-For` IP.
    - The `username` parameter.
- Set the attack type to **Pitchfork**.

### **6️⃣ Set Payloads**

- **Payload 1**: Numbers for the `X-Forwarded-For` IP (e.g., `10` to `110`).
- **Payload 2**: A username list (e.g., `username.txt`).

### **7️⃣ Use a Long Password String**

- Set the `password` parameter to a long string (e.g., `testtest...`).
- Analyze response times:
    - Valid usernames have longer response times.

### **8️⃣ Identify Valid Username**

- Look for a username with a significantly longer response time (e.g., `oracle` with `1500ms`).

### **9️⃣ Configure Intruder for Password Cracking**

- Send the updated request to Intruder.
- Place positional markers on:
    - The `password` parameter.
    - Another digit in the `X-Forwarded-For` header.

### **🔑 10️⃣ Identify the Correct Password**

- Use the identified username with a password list.
- Look for a **302 Found** response to confirm success.

### **🔓 11️⃣ Log in with Valid Credentials**

- Intercept the login request with Burp Suite.
- Modify the request:
    - Replace the username and password with valid credentials.
    - Add the `X-Forwarded-For` header and increment the IP.
- Forward the request to log in.

### **✅ 12️⃣ Confirm Completion**

- Successfully log in to complete the lab!

---

This updated format ensures you can glance at the first section and immediately grasp the lab's purpose and solution workflow, with deeper details below for reference.