### 📚 WebSocket Hijacking

---

### 🚀 High-Level Summary

**WebSocket Hijacking** occurs when an attacker gains unauthorized access to a WebSocket connection, allowing them to **send and receive data** on behalf of the victim. This often involves a **Cross-Site Request Forgery (CSRF)** attack during the **WebSocket Handshake** process, exploiting poor token validation or cookie-based authentication.

---

### 📦 Definition Bank

| **Term**                              | **Definition**                                                                    |
| ------------------------------------- | --------------------------------------------------------------------------------- |
| **WebSocket Hijacking**               | Unauthorized **access and control** of a WebSocket connection.                    |
| **CSRF (Cross-Site Request Forgery)** | Attacker tricks the victim into performing **unauthorized actions** on a web app. |
| **WebSocket Handshake**               | The **initial request** upgrading an HTTP connection to a WebSocket.              |
| **Session Token**                     | A **unique identifier** issued after successful authentication.                   |
| **Cookie-Based Auth**                 | Using **HTTP cookies** to maintain a user's authenticated session.                |

---

### 📡 How WebSocket Hijacking Works (Step-by-Step)

1. **User Authentication:**
    
    - The **user authenticates** and receives a **session token** stored in a **cookie**.
2. **User Visits a Malicious Site:**
    
    - The victim unknowingly visits a **malicious site** controlled by the attacker.
3. **WebSocket Handshake Exploitation:**
    
    - The malicious site **initiates a WebSocket connection** using the victim's **cookies** (if the server uses **cookie-based authentication** and lacks CSRF tokens).
4. **Session Hijacking:**
    
    - The attacker can now **send and receive** messages through the **WebSocket connection**, impersonating the victim.

---

### 🛠️ Example Attack Scenario (Visual Aid)
![[Pasted image 20250113015959.png]]
- **Step 1:** User logs into a legitimate site and receives a **session token** stored as a cookie.
- **Step 2:** User visits a **malicious website** designed by the attacker.
- **Step 3:** The malicious site performs a **WebSocket handshake** using the victim’s cookies.
- **Step 4:** The attacker gains **read/write access** to the WebSocket channel and can steal data or perform actions on the victim's behalf.

---

### 🕵️‍♂️ How to Identify WebSocket Hijacking Vulnerabilities

- **Check WebSocket Handshake:** Inspect whether the **handshake** relies on **cookies** for authentication.
- **CSRF Token Absence:** Verify if the server implements **CSRF tokens** during the handshake.
- **Token Reuse:** Confirm if tokens are **reused** after the initial handshake without revalidation.
- **Cookie Inspection:** Analyze if the **cookies** used are marked as **HttpOnly** and **Secure**.

---

### 🛡️ Preventing WebSocket Hijacking

- **Implement CSRF Tokens:**
    - Use **unique** and **validated** CSRF tokens during the **WebSocket handshake**.
- **Cookie Security:**
    - Mark cookies as **`HttpOnly`** and **`Secure`** to prevent **JavaScript access** and force **HTTPS**.
- **Token-Based Authentication:**
    - Use **JWT tokens** or other **header-based** authentication instead of cookies.
- **Same-Origin Policy:**
    - Enforce **CORS** policies to block unauthorized **cross-origin requests**.
- **Input Validation:**
    - Ensure **proper validation** of all incoming WebSocket messages.

---

### ✅ Key Takeaways

- **WebSocket Hijacking** allows **attackers** to control a WebSocket connection and impersonate users.
- It often occurs due to **CSRF vulnerabilities** and **cookie-based authentication**.
- Mitigation involves **CSRF tokens**, **secure cookies**, and **token-based authentication**.

---