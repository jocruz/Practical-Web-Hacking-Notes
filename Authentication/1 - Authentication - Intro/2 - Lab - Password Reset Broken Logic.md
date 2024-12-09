### **Introduction to Authentication: Password Reset Broken Logic**
## 🔗 Related Topics
- [[1 - Authentication Vulnerabilities Overview]]
#authentication #lab #password-reset

---

#### **Concept Notes**

**Authentication** is the process of verifying the identity of a user, application, or system. Common authentication methods include passwords, tokens, biometric scans, and multi-factor authentication (MFA). This lab demonstrates how weak implementation of password reset functionality can be exploited.

##### **Key Terms**

- **Broken Authentication**: Exploiting flaws in authentication mechanisms to gain unauthorized access.
- **Reset Token**: A temporary string used to validate password reset requests.
- **Burp Suite**: A tool used to intercept, analyze, and manipulate HTTP requests.

##### **Common Pitfalls**

- Not validating reset tokens properly.
- Allowing token reuse or tampering.
- Failing to bind tokens to specific accounts or expiration times.

---

#### **Lab Notes: Password Reset Broken Logic**

##### **Objective**

Exploit the password reset functionality to gain access to Carlos's account and solve the lab.

##### **Credentials**

- Your credentials: `wiener:peter`
- Victim's username: `carlos`

##### **Steps to Exploit**

1. **Initial Reconnaissance**
    
    - Log in with `wiener:peter` and navigate to the "Forgot your password?" page.
    - Enter the email `wiener@pw.com` and submit the form.
    - Open the **Email client** tab to access the reset link.
2. **Intercept and Analyze Requests**
    
    - Use **Burp Suite** to intercept the password reset process.
    - Study the `POST` requests, focusing on:
        - `temp-forgot-password-token`
        - Hidden input fields like `username`, `new-password`, and `new-password-2`.
3. **Test the Vulnerability**
    
    - Reset your own password using the reset link and confirm success.
    - In **Burp Repeater**, modify the `temp-forgot-password-token` value:
        - Delete its value or replace it with arbitrary text.
    - Observe that the reset functionality still works, indicating the token is not properly validated.
4. **Exploit the Vulnerability**
    
    - Request another reset token.
    - Intercept the `POST /forgot-password` request.
    - Modify the `username` parameter to `carlos` and set `new-password` to your desired password (e.g., `password`).
    - Send the request and confirm success with the `HTTP/2 302 Found` response.
5. **Validate Exploitation**
    
    - Log in as `carlos` using the new password.
    - Navigate to the "My Account" page to complete the lab.

---

#### **Key Takeaways**

1. **Reset Token Issues**:
    
    - Token reuse and tampering were possible.
    - Tokens were not validated, allowing arbitrary account access.
2. **Practical Applications**:
    
    - Always validate reset tokens server-side.
    - Bind tokens to specific accounts and set expiration times.
3. **Troubleshooting Tips**:
    
    - If token tampering doesn't work, check for hidden parameters in intercepted requests.
    - Ensure all request modifications are consistent (e.g., changing the token in multiple places).

---

#### **Related Topics**

- `[[Broken Authentication]]`
- `[[Burp Suite Notes]]`
- `[[HTTP Methods and Responses]]`

---

#### **Related Labs**

- `[[Lab: Username Enumeration via Response Timing]]`
- `[[Lab: SQL Injection in Authentication]]`

---

#### **Tools and Commands**

- **Burp Suite**:
    - Proxy: Intercept and analyze HTTP requests.
    - Repeater: Modify and resend requests for testing.
- **Common Commands**:
    - Modifying POST parameters in intercepted requests.
    - Observing server responses (e.g., `HTTP/2 302 Found`).

---

### **What to Do Next**

- Prepare a workflow for testing authentication mechanisms:
    
    1. **Reconnaissance**: Analyze input fields, hidden parameters, and server responses.
    2. **Exploitation**: Test token reuse, tampering, and improper validation.
    3. **Post-Exploitation**: Confirm account access and navigate sensitive areas (e.g., "My Account").
- Practice similar labs to reinforce concepts:
    
    - Test for token reuse vulnerabilities in other authentication workflows.
    - Explore related authentication attacks like brute forcing and session fixation.

---

#### **Visualization Suggestion**

Create a mind map showing the flow of password reset requests, highlighting weak points like token validation and tampering opportunities. Tag these notes with `#authentication`, `#burpsuite`, and `#password-reset`.