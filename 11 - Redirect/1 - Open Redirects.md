### 📚 Open Redirects

---

### 🚀 High-Level Summary

**Open Redirects** occur when a web application **forwards users** to a **URL** provided via **user input** without proper validation. This can be exploited by **attackers** to redirect users to **malicious websites**, enabling **credential theft**, **malware delivery**, and **phishing attacks**.

---

### 📦 Definition Bank

|**Term**|**Definition**|
|---|---|
|**Open Redirect**|A vulnerability where unvalidated user input determines a **redirect URL**.|
|**Query Parameter**|Part of a URL that passes data (`?redirectURL=dashboard`).|
|**DOM-Based Vulnerability**|A client-side vulnerability where the **DOM** processes untrusted inputs.|
|**Phishing Attack**|Tricking users into revealing credentials via **spoofed** web pages.|

---

### 📡 How Open Redirects Work (Step-by-Step)

1. **User Login:**
    - The user **authenticates** successfully on a trusted website.
2. **Legitimate Redirect Usage:**
    - The application redirects the user via a **query parameter** after login:
        - Example: `https://tcm-sec.com/login?redirectURL=https://tcm-sec.com/dashboard`.
3. **Crafting a Malicious Link:**
    - An attacker crafts a **malicious URL** and replaces the destination with a site they control:
        
        ```plaintext
        https://tcm-sec.com/login?redirectURL=https://malicious.com
        ```
        
4. **User Clicks Malicious Link:**
    - The victim clicks the link, trusting the **legitimate domain**, but gets redirected to **malicious.com**.
5. **Exploitation:**
    - The attacker can now **steal credentials**, **spread malware**, or perform **phishing attacks**.

---

### 🧩 Example Exploit Scenario

- **Original URL:**
    
    ```
    https://tcm-sec.com/login?redirectURL=https://tcm-sec.com/dashboard
    ```
    
- **Malicious URL:**
    
    ```
    https://tcm-sec.com/login?redirectURL=https://malicious.com
    ```
    

**Risk:** The victim may **trust** the `tcm-sec.com` domain and click without noticing the **redirect manipulation**.

---

### 🕵️‍♂️ How to Identify Open Redirect Vulnerabilities

- **Check Query Parameters:** Look for URLs with **`redirectURL`**, **`next`**, or **`returnTo`**.
- **Fuzzing:** Try replacing the redirect with an **external domain**.
- **DOM Analysis:** Inspect if the **DOM** directly processes unvalidated **user inputs**.
- **Code Review:** Identify improper handling of **redirect URLs** in server-side code.

---

### 🛡️ Preventing Open Redirect Vulnerabilities

- **Validate Inputs:**
    - Ensure all **redirect URLs** are restricted to **trusted domains** only.
- **Use Allowlists:**
    - Implement a **whitelist** of allowed domains for **redirect parameters**.
- **Remove Direct URL Control:**
    - Avoid **user-controlled** redirect parameters entirely when possible.
- **Tokenize Redirects:**
    - Use **secure tokens** instead of direct URLs for redirections.
- **Encode Output:**
    - Properly **encode** output to avoid **DOM-based** vulnerabilities.

---

### 🛠️ Secure Code Example (Node.js)

```javascript
const allowedURLs = ['https://tcm-sec.com/dashboard'];

app.get('/login', (req, res) => {
    const redirectURL = req.query.redirectURL;
    if (allowedURLs.includes(redirectURL)) {
        res.redirect(redirectURL);
    } else {
        res.status(400).send("Invalid redirect URL");
    }
});
```

**Why Secure?**  
✅ Only allows **predefined** redirect destinations.  
✅ Prevents **user-controlled redirects**.  
✅ Blocks unauthorized domains.

---

### ✅ Key Takeaways

- **Open Redirects** can be exploited for **phishing**, **malware delivery**, and **credential theft**.
- Attackers often use **user-controlled query parameters** for redirection.
- **Preventive measures** include **input validation**, **whitelisting**, and **tokenization**.
