# 🧪 Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

**Lab URL:** [https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)

## 🎯 High-Level Summary

**Objective:** Perform a DOM-based cross-site scripting (XSS) attack by injecting a payload that calls the `alert()` function. This vulnerability occurs due to unsanitized user input being written directly to the DOM using `document.write`.

**Key Techniques & Concepts:**

- **DOM-Based XSS:** The payload is injected into the Document Object Model (DOM) directly via JavaScript.
    
- **JavaScript Sinks:** Vulnerable usage of `document.write`.
    
- **URL Manipulation:** The payload is controlled via `location.search` in the URL.
    

**What Makes This Vulnerable?**

- The web application uses the `document.write` method to write unsanitized user input from `location.search` directly into the page, without proper escaping.
    
- The payload is reflected inside an `img` attribute, making it possible to break out and execute arbitrary JavaScript.
    

---

## 🧑‍💻 PortSwigger Solution

To solve this lab, perform a cross-site scripting attack that calls the `alert` function:

1. **Initial Test:** Enter a random alphanumeric string into the search box.
    
2. **Identify Reflection:** Right-click and inspect the element to confirm the random string appears inside an `img src` attribute.
    
3. **Exploit:** Break out of the `img` attribute with the following payload:
    
    ```
    "><svg onload=alert(1)>
    ```
    
4. **Execution:** Submit the payload and verify the alert box triggers, confirming the XSS vulnerability.
    

---

## 📚 Instructor Solution

### Using DOM Invader

1. Open **Burp Suite Community Edition** and navigate to:
    
    - **Proxy > Intercept > Browser**.
        
2. This opens a browser session where you can visit the lab URL.
    
3. Press **Ctrl + Shift + C** to open the developer console and enable **DOM Invader**.
    
4. Enter a unique canary value into the search bar on the web application.
    
5. DOM Invader will identify if the input is reflected in a vulnerable sink and display a green **Exploit** button.
    

**Screenshot:** ![[Pasted image 20250108001330.png]]

### Reviewing the Vulnerable Code Path:

1. Click the **stack trace** hyperlink in **DOM Invader** to view the source code location.

2. Identify the vulnerable code:
    
    ```
    document.write('img src="/resources/images/tracker.gif?searchTerms=tpem9by9">');
    ```

3. The payload lands inside:
    
    ```
    <img src="/resources/images/tracker.gif?searchTerms=tpem9by9">
    ```


### Final Exploit Payload:

Since the payload lands inside an `img` attribute, the following payload successfully breaks out and triggers the XSS:

```
"><img src=x onerror=prompt()> 
```

✅ **Lab completed successfully with this payload.**

---

## 💡 Explanation of Payloads and Techniques

**Why** `"><svg onload=alert(1)> **Works:**

- The payload closes the current `img src` attribute and introduces a new `svg` element.
    
- The `onload` event is triggered when the element loads, executing the `alert(1)` function.
    

**Why** `"><img src=x onerror=prompt()>` **Works:**

- The payload closes the `img` attribute and introduces a new invalid `src` value.
    
- The `onerror` event triggers the `prompt()` function when the image fails to load.
    

**Why Traditional Payloads Fail:**

- Angle brackets are encoded.
    
- The payload must escape the current HTML context safely and trigger JavaScript execution.
    

---

## 🛠️ Tools and Commands

- **Burp Suite Community Edition:** Repeater, DOM Invader
    
- **Payloads:**
    
    - `"><svg onload=alert(1)>`
        
    - `"><img src=x onerror=prompt()>`
        
- **Resources:** [PortSwigger XSS Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)