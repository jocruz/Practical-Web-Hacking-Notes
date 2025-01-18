
---

# 🔐 Password Reset Broken Logic

## **🛠️ Lab Goal**

- **Objective**: Test for improper token validation in password reset mechanisms and exploit weak logic to gain unauthorized account access (e.g., Carlos's account).

---

## 🚀 Solution Summary

### 1️⃣ Reconnaissance

- 🔍 Log in as `wiener:peter` and request a password reset link.
- 📨 Inspect the reset link and parameters via **Burp Suite** (e.g., `temp-forgot-password-token`, hidden fields).

### 2️⃣ Testing Token Validation

- ✅ Reset your password to verify the reset flow works as intended.
- ✏️ Modify the token (e.g., delete it, use an arbitrary value, or test common payloads like a blank or malformed JWT).

### 3️⃣ Exploitation

- 🕵️‍♂️ Request a new reset token and intercept the request.
- ✏️ Modify the `username` to `carlos` and set a new password.
- 💡 Confirm success with an HTTP `302 Found` response.

### 4️⃣ Validation

- 🔑 Log in as `carlos` using the new password.
- 🏁 Navigate to the "My Account" page to complete the lab.

---

## 📌 Key Takeaways

### 1️⃣ Core Testing Methodology

- *Token Validation*:
    
    - 🧪 Test if tokens are required, can be removed, or replaced with arbitrary values.
    - 🛠️ If tokens are **JWTs**, try tampering with payloads or signatures.
    - 🔄 Check for token reuse or expiration issues.
- *Payload Manipulation*:
    
    - 🔗 Intercept reset requests to modify parameters like `username` and `token`.
    - 📬 Look for hidden fields that can be manipulated.

### 2️⃣ Security Best Practices

- 🚫 Tokens must always be validated **server-side**.
- 📛 Bind tokens to specific users and enforce expiration times.
- 🔒 Use robust token generation methods to prevent guessing or tampering.

### 3️⃣ Exam Tips

- 🧐 Focus on hidden input fields and intercepted requests for vulnerabilities.
- 🔄 Ensure token modifications are consistent across all related parameters.
- 🔧 Troubleshoot using Burp Suite's **Repeater** to analyze server responses.

---

## 🧰 Practical Applications

- Use this lab's methodology to test **password reset flows** in real-world applications:
    
    - 🚩 Look for improper validation of tokens.
    - 🚩 Identify reuse or tampering opportunities.
    - 🚩 Test for insecure token storage or disclosure.
- Test other related attack vectors:
    
    - 🔄 **Session Hijacking**: Can session cookies be reused?
    - 🔒 **Brute Forcing Tokens**: Are tokens predictable or weakly generated?

---

## 🗂️ What to Do Next

### **1️⃣ General Testing Workflow**

1. **Reconnaissance**: Analyze input fields, hidden parameters, and server responses.
2. **Exploration**: Test tokens for tampering, reuse, and proper validation.
3. **Exploitation**: Modify requests to escalate privileges or gain unauthorized access.
4. **Post-Exploitation**: Confirm access and validate findings.

### 2️⃣ Practice Labs

- **Related Labs**:
    - 🕵️‍♂️ Username Enumeration via Response Timing.
    - 🛡️ SQL Injection in Authentication.

---

## 🧠 Visualization

- 🖼️ Create a flow diagram of the password reset process:
    - 📤 User submits reset request → receives token → uses token for reset.
    - 🚨 Highlight weak points like **improper token validation** and **modifiable parameters**.

---
