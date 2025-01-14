Lab URL:
https://portswigger.net/web-security/dom-based/open-redirection/lab-dom-open-redirection

Lab Description:

This lab contains a DOM-based open-redirection vulnerability. To solve this lab, exploit this vulnerability and redirect the victim to the exploit server.

Port Swigger Solution:

The blog post page contains the following link, which returns to the home page of the blog:

`<a href='#' onclick='returnURL' = /url=https?:\/\/.+)/.exec(location); if(returnUrl)location.href = returnUrl[1];else location.href = "/"'>Back to Blog</a>`

The `url` parameter contains an open redirection vulnerability that allows you to change where the "Back to Blog" link takes the user. To solve the lab, construct and visit the following URL, remembering to change the URL to contain your lab ID and your exploit server ID:

`https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/`

Instructor solution:

We go to the web application lab, we go to view one of the post, we land in a comment section, we then inspect the web page, and we inspect the Back to Blog button

from there we are able to see the following element:

```html
<a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : &quot;/&quot;">Back to Blog</a>
```

### ✅ **What the Element Does:**

```html
<a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : &quot;/&quot;">Back to Blog</a>
```

1. **When clicked:**
    - ✅ The `onclick` event triggers JavaScript.
2. **Regex Search:**
    - Searches the URL for `url=` followed by a link.
3. **If Found:**
    - ✅ Redirects to the captured URL (`returnUrl[1]`).
4. **If Not Found:**
    - 🚫 Redirects to the homepage (`/`).

---

From here we visit the exploit server button from the lab, we grab the exploit URL and we return back to the view post where the Back to Blog button is, from there we add &url=https: // exploit server url from lab

and we hit enter and we get a completed lab


### 📌 **Why Adding `&url=` Worked:**

Your modified link:

```plaintext
/post?postId=4&url=https://exploit-site.com
```

- ✅ The **`&url=`** made the regex **find a match**.
- ✅ `returnUrl[1]` now contains the malicious URL you provided.
- ✅ The link redirects users when clicked.

---

### 🚩 **Security Issue (Open Redirect):**

- Adding `&url=` allows **redirecting users** to any site.
- 🚨 **Risk:** Can be exploited for **phishing** or **malware delivery**.

✅ Let me know if you need help reporting this properly! 🚀