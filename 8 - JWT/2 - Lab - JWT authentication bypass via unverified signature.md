
---
# 🧪 Lab: JWT Authentication Bypass via Unverified Signature

Lab URL: [https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)

---

## High-Level Summary

**Objective:** Modify a JWT token to gain unauthorized access to the admin panel and delete the user `carlos`.

**Key Techniques & Concepts:**

- **JWT Structure:** Header, Payload, Signature
    
- **Unverified Signature Flaw:** Server fails to validate the token signature.
    
- **Token Manipulation:** Modify the `sub` claim for privilege escalation.
    

**What Makes This Vulnerable?**

- The server does not verify the signature of the JWT tokens, allowing attackers to modify claims without proper validation.
    

---

## PortSwigger Solution

1. **Log into the Lab:**
    
    - Use the provided credentials: `wiener:peter`.
        
2. **Capture the JWT:**
    
    - In **Burp Suite**, go to **Proxy > HTTP history**.
        
    - Locate the `GET /my-account` request after login.
        
    - Observe the `session` cookie containing a JWT.
        
3. **Decode the JWT:**
    
    - Double-click the JWT payload in **Burp Inspector**.
        
    - Review the token’s structure and claims (`sub` containing `wiener`).
        
4. **Modify the Token:**
    
    - Send the request to **Repeater (Ctrl+R)**.
        
    - Change the request path from `/my-account` to `/admin`.
        
    - Modify the `sub` claim from `wiener` to `administrator` using the JWT tab.
        
5. **Access the Admin Panel:**
    
    - Send the modified request and confirm access to the admin panel.
        
6. **Delete the User** `**carlos**`**:**
    
    - Locate the `/admin/delete?username=carlos` endpoint.
        
    - Send the request with the modified JWT to delete `carlos` and complete the lab.
        

---

## Instructor Solution

### Step 1: Log into the Web Application

- Visit the `/login` page and log in with the credentials `wiener:peter`.
    
- Navigate to **Burp Suite > Proxy > HTTP History**.
    

### Step 2: Inspect the JWT Token

- Look for the POST `/login` request where a JWT is issued.
    
- Identify the JWT components in the **JSON Web Token** tab:
    
    - **Header:** Algorithm (`alg`: RS256)
        
    - **Payload:** Subject (`sub`: wiener)
        
    - **Signature:** Base64-encoded signature
        

### Step 3: Modify the JWT Claim

- Send the `/my-account` request to **Repeater**.
    
- Change the `sub` claim from:
    
    ```
    "sub": "wiener"
    ```
    
    to:
    
    ```
    "sub": "administrator"
    ```
    
- Click **Apply changes** in Burp.
    

### Step 4: Bypass Authentication

- Send the modified request to `/admin`.
    
- Confirm access to the **Admin Panel**.
    

### Step 5: Complete the Lab

- Change the cookie in the browser:
    
    - Open **Developer Tools > Application > Cookies**.
        
    - Replace the existing `session` cookie with the modified JWT.
        
- Visit `/my-account` to verify successful access as the administrator.
    
- Delete the user `carlos` via the `/admin/delete?username=carlos` endpoint.
    

---

## Explanation of Payloads and Techniques

### **JWT Structure:**

A JSON Web Token (JWT) consists of three parts separated by periods (`.`):

1. **Header:** Specifies the algorithm used for signing the token (`RS256`).
    
2. **Payload:** Contains the claims, including `sub` (subject).
    
3. **Signature:** Verifies the authenticity of the token using a secret key.
    

### **Why the Attack Works:**

- The server **fails to validate the signature**, so modifying the payload is sufficient to escalate privileges.
    
- By changing the `sub` claim to `administrator`, the server grants unauthorized access.
    

### **Key Attack Variations:**

- **Removing the Signature:** Some misconfigured servers may not verify the signature at all.
    
- **Changing the Algorithm to** `**none**`**:** Switching the signing algorithm to `none` can bypass validation on vulnerable implementations.
    

---

## Tools and Commands

- **Burp Suite:** Repeater, JWT Editor Extension
    
- **Payload:**
    
    ```
    {
      "sub": "administrator"
    }
    ```
    
- **Tools Used:**
    
    - Burp Suite Professional
        
    - JWT Editor Extension
        
- **Resources:** [PortSwigger JWT Cheat Sheet](https://portswigger.net/web-security/jwt)