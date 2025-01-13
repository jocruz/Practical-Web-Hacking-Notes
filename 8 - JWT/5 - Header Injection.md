
---
### 📚 **JWT Header Injection (JWK, JKU, KID Explained)**

---

### 🚀 **High-Level Summary**

**Header Injection Attacks** in **JWTs** exploit improperly validated token headers to **manipulate the server’s key verification process**. By modifying headers like **JWK, JKU, and KID**, attackers can **bypass signature validation** and forge JWTs.

✅ **Key Insight:**

- If the server doesn't **properly validate the headers**, an attacker can **inject a public key** or direct the server to an external key they control.

---

### 📦 **Definition Bank**

|**Term**|**Definition**|
|---|---|
|**Header Injection**|Exploiting JWT headers to manipulate which key the server uses for verifying token signatures.|
|**JWK (JSON Web Key)**|A **JSON object** that includes a **public key** directly in the token header.|
|**JKU (JSON Web Key URL)**|A URL that links to a **set of public keys** stored externally, allowing the server to fetch the key for verification.|
|**KID (Key ID)**|A **key identifier** used when multiple keys are present, guiding the server on which key to use for validation.|

---

### 📡 **Key Concepts and Explanation**

---

#### 1. **What is Header Injection?**

- **Header Injection** occurs when **JWT headers** (like `JWK`, `JKU`, and `KID`) can be **manipulated** to control which **public key** the server uses for verifying a JWT's signature.
- If the server **trusts external keys** or fails to verify the injected key, an attacker can **forge a token** and get it accepted as valid.

✅ **Goal:** Trick the server into using a **key we control** for token validation.

---

#### 2. **JWK (JSON Web Key) Injection**

The **JWK (JSON Web Key)** header allows an attacker to embed a **public key** directly inside the token.

✅ **Standard Behavior (Secure)**:

- Server verifies the token with a **trusted key** stored internally.

✅ **Exploitation:**

- Attacker injects their **own public key** in the JWT header.
- If the server uses the **injected key** without verification, it could allow **token forgery**.

**Example:**

```json
{
  "alg": "RS256",
  "typ": "JWT",
  "jwk": {
    "kty": "RSA",
    "n": "MIIBIjANBgkqhkiG9w...",
    "e": "AQAB"
  }
}
```

✅ **Key Insight:**

- If the server uses the **embedded key** without verifying it against a trusted key set, the attacker can **forge a valid token**.

---

#### 3. **JKU (JSON Web Key URL) Injection**

The **JKU (JSON Web Key URL)** header allows a **URL reference** to a set of public keys.

✅ **Standard Behavior (Secure)**:

- The server fetches the **trusted public key** from a **secure URL** for validation.

✅ **Exploitation:**

- Attacker hosts a **malicious key** at a URL they control.
- The server **fetches the attacker-controlled key**, allowing **token forgery**.

**Example:**

```json
{
  "alg": "RS256",
  "typ": "JWT",
  "jku": "https://evil.com/malicious-jwks.json"
}
```

✅ **Key Insight:**

- If the server doesn't **validate the URL**, it may download a **malicious key** controlled by the attacker.

---

#### 4. **KID (Key ID) Injection**

The **KID (Key ID)** header specifies which **key** the server should use if multiple keys are present.

✅ **Standard Behavior (Secure):**

- The server verifies the **KID** against a **predefined** set of trusted keys.

✅ **Exploitation:**

- Attacker modifies the `KID` value to **point to an invalid key**.
- If the server fails to validate the KID properly, it might **default to a weak or no verification state**.

**Example:**

```json
{
  "alg": "HS256",
  "typ": "JWT",
  "kid": "none"
}
```

✅ **Key Insight:**

- If the server accepts `"none"` as a valid key identifier, it could skip signature verification altogether.

---

### 📡 **How Header Injection Works (Exploit Process)**

1. **Inspect the Token:** Identify if the token includes a `JWK`, `JKU`, or `KID` header.
2. **Modify the Header:** Inject a **malicious public key** or a reference to a **controlled URL**.
3. **Re-sign the Token:** Use tools like **jwt.io** to modify and resign the token.
4. **Send the Token:** Submit the modified token to the server.
5. **Observe:** If accepted, the server has **improperly validated the key**.

---

### 🎯 **Pen Testing Use Cases for Header Injection**

|**Use Case**|**Objective**|**How to Test**|
|---|---|---|
|**JWK Injection**|Add a public key controlled by the attacker.|Modify JWT header and test if the server accepts it.|
|**JKU Injection**|Redirect the server to a malicious key URL.|Point `jku` to an attacker-controlled URL.|
|**KID Manipulation**|Use an invalid KID or attempt `none`.|Modify the `kid` header and test if validation is skipped.|

---

### 🛡️ **Defenses Against Header Injection Attacks**

✅ **Key Validation:** Always validate keys against a **trusted key set**.  
✅ **Reject External URLs:** Block **JKU references** to untrusted external sources.  
✅ **Disable Weak Algorithms:** Avoid using **HS256** with weak keys.  
✅ **Use Strong Key Management:** Use **asymmetric encryption** (RSA).  
✅ **Enforce KID Validation:** Ensure the `KID` references a **valid key** only.

---

### ✅ **Example: Secure JWT Using Proper Key Management (RS256)**

```json
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "12345"
}
```

✅ **Why Secure?**

- Uses **asymmetric encryption** (`RS256`).
- **KID** is properly validated against **trusted keys**.
- **No external key URLs.**

---

### ✅ **Summary of Exploit Steps (Simplified)**

1. **Identify Vulnerability:** Check if the token uses **JWK, JKU, or KID**.
2. **Inject a Malicious Key:**
    - For **JWK**: Insert a public key directly.
    - For **JKU**: Link to a malicious hosted key.
    - For **KID**: Change the `KID` to bypass validation.
3. **Re-sign and Test the Token:**
    - Use **jwt.io** to modify and test the token.
4. **Confirm Bypass:** If the token is **accepted**, the server fails **proper key validation**.

---

### 🔗 **Additional Resources:**

- 🔗 [JWT.io Token Editor & Decoder](https://jwt.io/)
- 🔗 [RFC 7517 - JSON Web Key (JWK) Specification](https://datatracker.ietf.org/doc/html/rfc7517)

---

### ✅ **Final Takeaways:**

- **Header Injection** allows attackers to manipulate how **JWTs** are validated.
- Always **validate** keys against a **trusted source**.
- Understand the differences between **JWK, JKU, and KID**.