## 🧪 Lab: DOM-Based Open Redirection
---
**🔗 Lab URL:** [Web Security Academy](https://portswigger.net/web-security/dom-based/open-redirection/lab-dom-open-redirection)

## 🎯 High-Level Summary

**✅ Objective:** Exploit a DOM-based open redirection vulnerability to redirect the victim to an external site using the exploit server.

**🛠️ Key Techniques & Concepts:**

- **DOM Manipulation:** Vulnerability originates from unvalidated URL handling in JavaScript.
    
- **Open Redirect:** Flawed use of `location.href` in the `onclick` event handler.


**⚠️ What Makes This Vulnerable?**

- The application allows arbitrary redirection through user-controlled input (`url` parameter) without validation.


---

## 🛡️ PortSwigger Solution

1. **Identify the Vulnerability:**
    
    - Go to the blog post page and inspect the **Back to Blog** button.
        
    - The following HTML element is present:
    
    
    ```
    <a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : '/';">Back to Blog</a>
    ```
    
2. **Analyze the Flaw:**
    
    - The `onclick` event searches for the `url=` parameter using a regular expression.
        
    - If the parameter exists, it redirects the user to the URL provided.
        
3. **Craft the Exploit:**
    
    - Construct a URL that includes a malicious redirect:
        
        ```
        https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/
        ```
        
4. **Execute the Exploit:**
    
    - Paste the modified URL in the address bar and press enter.
        
    - Verify the redirect to your exploit server.
    

---

## 🎓 Instructor Solution

### 🕵️ Step 1: Inspect the Web Page

- Visit the web application and navigate to a blog post.
    
- Locate the **Back to Blog** button and inspect the element.


### 📜 Step 2: Analyze the JavaScript Code

```
<a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : '/';">Back to Blog</a>
```

**Breakdown:**

- **Regex Matching:** Searches the URL for a `url=` parameter.
    
- **If Found:** Redirects to the provided URL.
    
- **If Not Found:** Redirects to `/` (homepage).
    

### 🚀 Step 3: Craft the Exploit

- Open the **Exploit Server** and copy the exploit server URL.
    
- Modify the blog post URL as follows:


```
https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/
```

### ✅ Step 4: Verify the Exploit

- Paste the crafted URL into the browser and press **Enter**.
    
- Confirm successful redirection to the exploit server, completing the lab.


---

## 🔎 Explanation of the Regex

**Regex Used:**

```
/url=(https?:\/\/.+)/
```

### 🧩 Regex Breakdown:

- `url=`: Matches the string `url=`.
    
- `https?`: Matches `http` or `https`.
    
- `:\/\/`: Escaped slashes to match `://`.
    
- `. +`: Matches the rest of the URL.


### ✅ Why Hitting Enter Works:

- The JavaScript automatically executes the `location.href` update when the URL is modified.
    
- Manually entering the URL with the payload triggers the vulnerable regex match without needing to click the **Back to Blog** button.


---

## 📦 Tools and Commands

- **🧰 Burp Suite:** Inspect and modify the `url` parameter.
    
- **🌐 Web Browser:** Test URL redirection manually.
    
- **💥 Exploit Server:** Deliver and verify the exploit.


---

## ⚠️ Security Implications

- **Phishing Risk:** Users can be redirected to fake login pages.
    
- **Data Exfiltration:** Sensitive information could be leaked to external domains.
    
- **✅ Mitigation:** Validate and restrict redirect URLs to trusted domains only.