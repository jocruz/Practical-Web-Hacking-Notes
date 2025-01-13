--- 
# 🧪 Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

**Lab URL:** [https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)

## 🎯 High-Level Summary

**Objective:** Perform a DOM-based cross-site scripting (XSS) attack by injecting a payload that breaks out of a `select` element and calls the `alert()` function. The vulnerability arises from unsanitized user input being written directly to the DOM via `document.write`.

**Key Techniques & Concepts:**

- **DOM-Based XSS:** The payload is injected into the Document Object Model (DOM) directly via JavaScript.
    
- **JavaScript Sinks:** Vulnerable use of `document.write`.
    
- **URL Manipulation:** The payload is controlled via the `location.search` parameter.
    

**What Makes This Vulnerable?**

- The web application uses the `document.write` method to write unsanitized user input from `location.search` directly into the page without proper sanitization.
    
- The payload is reflected inside a dynamically generated `<select>` element, making it possible to break out and inject arbitrary JavaScript.
    

---

## 🧑‍💻 PortSwigger Solution

1. **Identify Vulnerable Code:**
    
    - On the product pages, the application extracts a `storeId` parameter from the `location.search` source.
        
    - The value is then written directly into a dynamically generated dropdown (`select`) using `document.write`.
        
2. **Test the Reflection:**
    
    - Add a `storeId` parameter to the URL and enter a random alphanumeric string.
        
    - Example: `product?productId=1&storeId=test123`
        
    - Confirm the value is reflected as an option in the dropdown list.
        
3. **Exploit Construction:**
    
    - Modify the URL with the following payload to break out of the `select` element:
        
        ```
        product?productId=1&storeId="\></select><img src=1 onerror=alert(1)>
        ```
        
4. **Verify:**
    
    - Submit the payload and observe the alert box triggering, confirming successful XSS.
        

---

## 📚 Instructor Solution

### Inspecting the Web Page

1. Visit the product page and inspect the source using **Ctrl+U**.
    
2. Locate the relevant script block, which contains the following vulnerable code:
    

```
<script>
var stores = ["London","Paris","Milan"];
var store = (new URLSearchParams(window.location.search)).get('storeId');
document.write('<select name="storeId">');
if(store) {
    document.write('<option selected>'+store+'</option>');
}
for(var i=0;i<stores.length;i++) {
    if(stores[i] === store) {
        continue;
    }
    document.write('<option>'+stores[i]+'</option>');
}
document.write('</select>');
</script>
```

3. **Key Issue:** The `storeId` parameter from the URL is directly inserted into the page using `document.write` without any sanitization.
    

### Exploit Construction

1. Modify the URL to inject a malicious payload:
    
    ```
    product?productId=1&storeId="\></select><img src=1 onerror=alert(1)>
    ```
    
2. This payload breaks out of the `select` element and injects a new image tag with an invalid `src` value that triggers the `onerror` event.
    

✅ **The alert box appears, completing the lab.**

---

## 💡 Explanation of Payloads and Techniques

### **What the Script Does:**

1. **Generates a** `**<select>**` **dropdown dynamically:**
    
    - Opens the dropdown with `document.write('<select name="storeId">')`.
        
2. **Checks for the** `**storeId**` **parameter:**
    
    - If found, writes the provided value into an `<option>` tag:
        
        ```
        if(store) {
            document.write('<option selected>'+store+'</option>');
        }
        ```
        
3. **Adds Predefined Options:**
    
    - Loops through an array of city names and adds them as `<option>` tags.
        

### **Why the Payload Works:**

- **Context Escape:**
    
    - The payload `"></select>` **closes the** `**<option>**` **and** `**<select>**` **tags prematurely.**
        
- **JavaScript Injection:**
    
    - Injects a new element with an invalid image source and an `onerror` event handler.
        
- **JavaScript Execution:**
    
    - The `onerror=alert(1)` triggers an alert when the image fails to load.
        

### **Why This is Vulnerable:**

- Untrusted data from the URL (`storeId`) is inserted into the DOM using `document.write`.
    
- No sanitization or encoding is performed before writing user input into the page.
    

---

## 🛠️ Tools and Commands

- **Burp Suite Community Edition:** Repeater, DOM Invader
    
- **Payloads:**
    
    - `product?productId=1&storeId="\></select><img src=1 onerror=alert(1)>`
        
- **Resources:** [PortSwigger XSS Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)