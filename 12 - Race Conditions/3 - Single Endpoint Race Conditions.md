
---

# 🏁 Single Endpoint Race Conditions

## High-Level Summary

A **Single Endpoint Race Condition** occurs when multiple requests target the same endpoint concurrently, leading to a **collision** where actions overlap improperly. This can result in critical security issues, such as manipulating password resets to send tokens to an attacker instead of the legitimate user.

---

## Definition Bank

|**Term**|**Definition**|
|---|---|
|**Race Condition**|A situation where multiple processes try to access and modify shared data simultaneously, leading to unpredictable behavior.|
|**Race Window**|The time period between when a resource is checked and when it is updated. Exploiting this window allows attackers to interfere with data consistency.|
|**Session**|A temporary storage used by web servers to store information about a user.|
|**Token**|A secure, unique value used to authenticate a user during sensitive actions like password resets.|
|**Parallel Requests**|Sending multiple requests simultaneously to exploit timing issues in web applications.|

---

### ✅ How Single Endpoint Race Conditions Work (Simplified Example)

1. **Hacker Sends Two Password Reset Requests in Parallel:**
    
    - **Request 1:** For **[hacker@test.com](mailto:hacker@test.com)**.
    - **Request 2:** For **[victim@test.com](mailto:victim@test.com)**.
2. **User ID Overwritten (Race Window):**
    
    - **Request 1:** The server stores **[hacker@test.com](mailto:hacker@test.com)** in the session.
    - **Request 2:** Before Request 1 finishes, the server overwrites the session with **[victim@test.com](mailto:victim@test.com)**.
3. **Reset Token Generation:**
    
    - **Request 2:** The server generates a **password reset token** for **[victim@test.com](mailto:victim@test.com)**.
4. **Token Collision:**
    
    - **Request 1:** The server accidentally **sends the victim’s token** to **[hacker@test.com](mailto:hacker@test.com)** since the session was overwritten.

---

✅ **Final Result:**

- The hacker receives a **valid reset link** for **[victim@test.com](mailto:victim@test.com)**, allowing them to reset the victim's password and take over the account. 🚩

---

## 🖼️ Race Window Explained with the Image

![[Pasted image 20250113153648.png]]

### What the Image Shows:

- **Request 1:** Begins and sets the user to **hacker**.
- **Request 2:** Arrives during the race window and overwrites the user to **victim**.
- **Token Generation:** The server sends **two different tokens** to the hacker and victim due to the race window.

### **What is the Race Window?**

- The **race window** is the vulnerable time period between:
    - **Time of Check:** When the system verifies the initial condition (e.g., if the coupon or token is valid).
    - **Time of Use:** When the system updates or uses that condition.
- If multiple requests are made **during the race window**, they may interact with the same data before it gets updated properly, causing a **collision**.

---

## How to Identify Race Conditions

- **Parallel Requests:** Send multiple requests targeting the same endpoint simultaneously.
- **Timing Attacks:** Monitor delays in responses to spot timing inconsistencies.
- **Code Review:** Look for shared resource handling and token storage logic.
- **Burp Suite Tools:** Use **Turbo Intruder** or **HTTP Request Smuggler** for automated race condition testing.

---

## Exploiting Single Endpoint Race Conditions (Steps)

1. **Identify the Target:** Find endpoints handling sensitive actions like password resets.
2. **Send Parallel Requests:** Use tools like **Burp Suite's Turbo Intruder** to send concurrent requests.
3. **Observe Behavior:** Look for:
    - Tokens or coupons being reused.
    - Incorrect user data being stored or sent.
4. **Confirm Exploitability:** Verify if you can control the token or data delivered.

---

## Real-World Example (Password Reset)

1. Attacker sends a **password reset request** for both themselves and a victim.
2. **Request 1:** The server generates a token for the victim.
3. **Request 2:** The server overwrites the session and sends the token to the attacker.
4. Attacker gains unauthorized access to the victim’s account.

---

## 🛡️ Prevention & Mitigation

- **Atomic Transactions:** Ensure each request completes before processing the next one.
- **Token Validation:** Regenerate tokens after every action.
- **Locking Mechanisms:** Prevent simultaneous access to shared resources.
- **Rate Limiting:** Restrict multiple requests from the same user within a short time frame.

---

✅ **Summary:**  
Race conditions, specifically **Single Endpoint Race Conditions**, occur when multiple requests target a single endpoint during a vulnerable **race window**, causing data collisions. Web testers exploit this by sending parallel requests to endpoints handling sensitive operations like password resets. Proper **timing** and **resource locking** are critical for preventing these vulnerabilities.

---
