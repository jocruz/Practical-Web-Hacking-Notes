# 📖 Intro to XSS

## 📌 High-Level Summary

**Cross-Site Scripting (XSS)** is a **web vulnerability** where an attacker can inject **malicious JavaScript code** into a web application, which executes in the victim's browser. **XSS remains prevalent** despite modern defenses, frequently appearing in bug bounties and penetration tests. **Attack vectors** include **stealing session tokens**, **logging keystrokes**, and **exfiltrating autofill passwords**. Understanding the **different XSS types** and **execution contexts** is critical for both **defense and exploitation**.

---

## 📚 Definition Bank

|**Term**|**Definition**|
|---|---|
|**Cross-Site Scripting (XSS)**|A web security vulnerability allowing attackers to inject malicious scripts into websites viewed by users.|
|**Reflected XSS**|An XSS variant where the payload is included in a response only after a crafted request. The payload is **not stored**.|
|**Stored XSS**|An XSS variant where the payload is **permanently stored** (e.g., in a database) and executed whenever a user loads the vulnerable page.|
|**DOM-Based XSS**|A client-side XSS where the payload is executed without interacting with the server, often through JavaScript code.|
|**Payload**|Malicious code injected to perform actions like stealing data or hijacking user sessions.|
|**Context**|The location where the payload gets injected and executed (HTML tags, attributes, scripts).|

---

## 📌 Key Concepts and Explanations

### 1. Types of XSS Attacks:

- **Reflected XSS:**
    
    - Payload reflected in the server's response.
        
    - **Scope:** Limited to crafted requests.
        
- **Stored XSS:**
    
    - Payload stored (e.g., in a database).
        
    - **Scope:** Persistent impact on all visitors.
        
- **DOM-Based XSS:**
    
    - No server interaction.
        
    - **Scope:** Vulnerability within the browser.
        

### 2. Common Payloads and Execution Contexts:

- **HTML Context:** Injection into HTML tags (`<script>` tag).
    
- **Attribute Context:** Injecting payloads into element attributes (`onerror`, `onclick`).
    
- **Script Context:** Injecting directly into JavaScript code.
    

### 3. Basic Payload Example:

```
<script>alert('XSS Vulnerability!')</script>
```

**Explanation:** This payload injects a basic JavaScript alert box when executed, proving script injection is possible.

---

## ⚖️ Comparisons and Alternatives

|   |   |   |
|---|---|---|
|**Approach**|**Secure?**|**Explanation**|
|**Input Validation**|✅|Sanitizes input to prevent malicious code injection.|
|**Output Encoding**|✅|Escapes special characters to neutralize JavaScript.|
|**WAF (Web Application Firewall)**|❌|Can be bypassed using obfuscation techniques.|

---

## ✅ Best Practices for Secure Coding

- **Validate Input:** Always validate and sanitize user inputs to prevent injection attacks. Use whitelisting where possible and reject unexpected characters.
    
- **Encode Output:** Prevent cross-site scripting (XSS) by encoding data before rendering it on the page. Use libraries like **DOMPurify** for secure output encoding.
    
- **Content Security Policy (CSP):**
    
    - **What is CSP?** A **Content Security Policy (CSP)** is a security feature that helps prevent XSS attacks by specifying which sources of content can be trusted and executed.
    - **How is it used?** CSP is implemented using the `Content-Security-Policy` HTTP header. Example:
```https 
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.cdn.com
```
        
    - **Effect:** This restricts script execution to only those from the defined trusted sources.
- **Avoid Dangerous Functions:**
    
    - Do **not** use functions like `eval()`, `innerHTML`, or `document.write()` as they can lead to code execution vulnerabilities.
    - Instead, use **secure alternatives** like `textContent` or libraries that safely manipulate the DOM.

### 📌 Recommended Order of Implementation:

1. **Input Validation & Sanitization** – Prevent bad data from entering the system.
2. **Output Encoding** – Encode data before rendering to prevent XSS.
3. **CSP Implementation** – Add an additional layer of defense with CSP headers.
4. **Avoid Dangerous Functions** – Eliminate risky JavaScript practices altogether.

---

## 💡 Secure Code Example

```
const userInput = document.getElementById('inputField').value;
const sanitizedInput = DOMPurify.sanitize(userInput);
document.getElementById('outputField').innerText = sanitizedInput;
```

**Explanation:**

- **DOMPurify** is a **trusted library** for sanitizing user inputs.
    
- **innerText** is used instead of `innerHTML` to prevent executing scripts.
    
- This approach **mitigates XSS** by preventing script injection.