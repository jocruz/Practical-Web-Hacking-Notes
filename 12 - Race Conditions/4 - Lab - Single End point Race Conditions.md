# 🧪 Lab: Single-endpoint Race Conditions

**Lab URL:**  
[https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability](https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability)

## 📝 Lab Description

This lab's email change feature contains a **race condition** that enables you to associate an arbitrary email address with your account.

Someone with the address `carlos@ginandjuice.shop` has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.

### ✅ To Solve the Lab:

- Identify a race condition that lets you claim an arbitrary email address.
    
- Change your email address to `carlos@ginandjuice.shop`.
    
- Access the admin panel.
    
- Delete the user `carlos`.
    

You can log in to your own account using the credentials: `wiener:peter`.

---

## 🧩 PortSwigger Solution

### **Predict a Potential Collision**

1. Log in and attempt to change your email to `anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net`. Observe that a confirmation email is sent to your intended new address, and you're prompted to click a link containing a unique token to confirm the change.
    
2. Complete the process and confirm your email address has been updated.
    
3. Try submitting two different emails in succession and check the email client.
    
4. If the first confirmation link becomes invalid, it indicates the server **only stores one pending email address at a time**. This points to a **race condition vulnerability**.
    

### **Benchmark the Behavior**

1. Send the `POST /my-account/change-email` request to **Repeater**.
    
2. Create a **new tab group** by selecting the request and clicking the `+` button.
    
3. Duplicate the request **9 times** (total of 10 tabs).
    
4. Modify each request with a unique email address, such as:
    
    - `john1@exploit-...`
        
    - `john2@exploit-...`
        
    - ... up to `john10@exploit-...`
        
5. Send the group **in sequence** to verify the behavior. Observe a confirmation email for each request sent.
    

### **Probe for Clues**

1. Resend the group, but this time in **parallel**.
    
2. Check the email client. Observe that some confirmation emails were sent to the wrong addresses, indicating a **race condition** during parallel requests.
    

---

## 🎯 **Prove the Concept**

1. Create a new group in **Repeater** with two requests.
    
2. **Request 1:** Use an arbitrary email (`test@exploit-...`).
    
3. **Request 2:** Use the victim's email: `carlos@ginandjuice.shop`.
    
4. Send the group **in parallel** and click the send button multiple times to maximize the chance of winning the race condition.
    
5. Check the email client. If the confirmation email sent to your `test@exploit-...` contains a confirmation link for `carlos@ginandjuice.shop`, click it to confirm the change.
    
6. Return to the web app and refresh the **My Account** page. Verify that your email has changed to `carlos@ginandjuice.shop`.
    
7. Access the **Admin Panel** and delete the user `carlos` to complete the lab.
    

---

## 🧑‍🏫 **Instructor Solution**

1. **Log into the Lab:** Using `wiener:peter`.
    
2. **Capture Traffic:** Add an item to your cart, change the email to `john@exploit-...`, and capture the `POST /my-account/change-email` request in **Burp Suite**.
    
3. **Send to Repeater:** Send the captured request to **Repeater** and duplicate it **9 times**.
    
4. **Create Tab Group:** Select all requests, create a tab group named **Email Race Condition**.
    
5. **Send Parallel Requests:** Send the group **in parallel**.
    
6. **Check for Race Condition:** Observe mismatched email confirmations.
    
7. **Modify for Exploit:** Change the email to `test@exploit-...` and `carlos@ginandjuice.shop` in two requests.
    
8. **Spam Parallel Requests:** Send multiple parallel requests until the confirmation link for `carlos@ginandjuice.shop` arrives in your test inbox.
    
9. **Confirm:** Click the link, gain admin access, and delete `carlos`.
    

---

## 📌 **Explanation of the Race Condition**

**Why This Worked:**

- The server processes multiple email change requests simultaneously.
    
- **Database Update Delay:** There's a slight delay between when the server **receives a request** and **updates the database**.
    
- Sending **parallel requests** causes multiple entries to be updated at the same time, leading to an **overwrite**.
    

---

## 🧑‍💻 **Tools and Commands Used:**

- **Burp Suite:** Proxy, Repeater, Group Tabs, Parallel Request Sending
    
- **Key Concepts:** Race Conditions, Email Verification
    

---

✅ **Lab Completed Successfully!**