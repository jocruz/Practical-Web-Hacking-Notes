# Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element

Descirption:
This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search` which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the `alert` function.

Portswigger solution:
1. On the product pages, notice that the dangerous JavaScript extracts a `storeId` parameter from the `location.search` source. It then uses `document.write` to create a new option in the select element for the stock checker functionality.
2. Add a `storeId` query parameter to the URL and enter a random alphanumeric string as its value. Request this modified URL.
3. In the browser, notice that your random string is now listed as one of the options in the drop-down list.
4. Right-click and inspect the drop-down list to confirm that the value of your `storeId` parameter has been placed inside a select element.
5. Change the URL to include a suitable XSS payload inside the `storeId` parameter as follows:
    
    `product?productId=1&storeId="></select><img%20src=1%20onerror=alert(1)>`


Instructor Solution:


We inspect the web page, we inspect an item, we notice the webpage has
.../product?productId=1

and we do (ctrl+u)

From here, we inspect the source code, heading down to the script portion of the script we find this


```javascript
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

We return to the web page URL and we enter the pay load `product?productId=1&storeId="></select><img%20src=1%20onerror=alert(1)>`

The instructor says its important to look at the source code, use dom invader and other tools, no point in reading lines and lines of JS, look for source code that looks weird, if its doing something normal its probably fine, if its doing something funny then its probably not normal.


### **Summary of Why the Payload Works and What the Script Does:**

### **What the Script Does:**

1. **Generates a dropdown list (`<select>`) dynamically:**
    - Opens a `<select>` tag with `document.write('<select name="storeId">')`.
2. **Checks for a `storeId` in the URL:**
    
    - If found, it writes an `<option>` tag with the provided value as the default selected option.
    
    ```javascript
    if(store) {
        document.write('<option selected>'+store+'</option>');
    }
    ```
    
3. **Loops through a predefined list (`stores`) and adds options:**
    - Each city from the `stores` array is added as an `<option>` unless it matches the current `storeId`.
4. **Closes the `<select>` tag:**
    - The dropdown is closed using `document.write('</select>')`.

---

### **Why the Payload Works (XSS Explanation):**

The payload:

```plaintext
product?productId=1&storeId="></select><img src=1 onerror=alert(1)>
```

1. **Breaking out of the context:**
    - The attacker injects `"></select>` which **closes the `<option>`** and **forcibly ends the `<select>` tag** early.
2. **Injecting malicious HTML:**
    - After closing the dropdown, a malicious `<img>` tag is injected:
        
        ```html
        <img src=1 onerror=alert(1)>
        ```
        
3. **JavaScript Execution:**
    - The `onerror=alert(1)` executes when the image fails to load (`src=1` is invalid).

### **Key Issue:**

- The script **directly injects untrusted user input** (`storeId`) into the page using `document.write()` without sanitization, allowing the attacker to inject arbitrary HTML and JavaScript code.

✅ **Core XSS Mechanism:** The payload **breaks out of the dropdown list context** and injects a new HTML element with executable JavaScript.










