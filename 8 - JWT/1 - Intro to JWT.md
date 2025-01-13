### 📚 **JSON Web Tokens (JWTs) – Explained for Web Application Pen Testing**

---

### 🚀 **High-Level Summary**

**JSON Web Tokens (JWTs)** are a widely used **stateless** token format for securely transmitting information between parties. JWTs are **digitally signed**, often using **HMAC SHA256** or **RSA** for integrity and authenticity.

✅ **Use Case:** JWTs are commonly used for **authentication** in web applications, allowing users to stay logged in without server-side sessions.

---

### 📦 **Definition Bank**

|**Term**|**Definition**|
|---|---|
|**JWT (JSON Web Token)**|A compact, self-contained token for transmitting information securely between parties.|
|**Header**|Contains **token type (JWT)** and **signing algorithm** used (e.g., HMAC, RSA).|
|**Payload (Claims)**|Contains **user data** and additional claims like user roles, expiration, and issuer information.|
|**Signature**|A **cryptographic signature** ensuring the token hasn’t been tampered with.|
|**Base64 Encoding**|JWT components are **Base64-encoded**, making them readable but not encrypted.|

---

### 📡 **Key Concepts and Structure of a JWT**

A **JWT** consists of three parts, separated by dots (`.`):

```plaintext
HEADER.PAYLOAD.SIGNATURE
```

**Example JWT (Shortened for Clarity):**

```plaintext
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvZSIsImlhdCI6MTUxNjIzOTAyMn0.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

---

### 📦 **1. Header (Token Metadata)**

The **header** contains metadata about the token. It specifies the **algorithm** used for signing the token and the token type.

```json
{
  "alg": "HS256",  // HMAC SHA256 algorithm
  "typ": "JWT"      // Token type
}
```

✅ **Pen Test Insight:**

- **HS256 (HMAC)** is a symmetric algorithm, vulnerable if **secrets** are weak.
- **RSA (RS256)** is asymmetric and more secure if implemented properly.

---

### 📦 **2. Payload (Data & Claims)**

The **payload** contains the **claims** or **data** being transmitted.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "exp": 1714044800
}
```

✅ **Key Claims:**

- **`sub` (Subject):** Identifies the user.
- **`exp` (Expiration):** Token expiration (UNIX timestamp).
- **`iat` (Issued At):** Token creation timestamp.
- **`admin` (Custom Claim):** Custom roles or permissions.

✅ **Pen Test Insight:**

- Tokens can be **manipulated** by altering claims (e.g., changing `"admin": false` to `"admin": true`).
- **Replay Attacks:** If `exp` is missing or not checked, tokens can be reused indefinitely.

---

### 📦 **3. Signature (Token Integrity)**

The **signature** ensures the token hasn’t been altered and is created by combining the **header, payload**, and a **secret key**:

```plaintext
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

✅ **Pen Test Insight:**

- If the **secret key** is weak, an attacker can **brute-force** or guess it.
- Removing the signature (`none` algorithm) can allow **signature bypass** attacks.

---

### 🎯 **How JWTs Are Used in Web Applications**

JWTs are commonly used for:

- ✅ **Authentication:** Users receive a JWT upon login, included in subsequent requests as a `Bearer` token in the `Authorization` header.
- ✅ **Session Management:** Stateless — the server **validates the token** instead of storing a session.
- ✅ **Data Exchange:** Sharing information securely between different parts of an app or third parties.

---

### 🛠️ **Using `jwt.io` to Decode and Inspect Tokens**

The website [jwt.io](https://jwt.io/) can be used to **decode, verify, and generate** JWTs.

✅ **Steps for Decoding a Token:**

1. Go to [jwt.io](https://jwt.io/).
2. Paste the **JWT token** into the input box.
3. The **Header, Payload, and Signature** will be displayed in plaintext.

✅ **Pen Tester Use Case:**

- **Identify Weak Secrets:** If the token is signed with **HS256**, test for weak secrets using **John the Ripper** or **Hashcat**.
- **Tamper Payload:** Change the `"admin": false` to `"admin": true` and attempt replay.
- **Algorithm Abuse:** Try **none** algorithm attacks to bypass signature checks.

---

### 🧪 **JWT Security Risks for Web App Pen Testers**

|**Vulnerability**|**Impact**|**How to Test**|
|---|---|---|
|**Weak Secret Keys (HS256)**|Allows **brute-forcing** the secret and token forging.|Use tools like **John the Ripper** or **Hashcat**.|
|**No Expiration (`exp`) Check**|Tokens can be **reused indefinitely**, leading to session hijacking.|Remove the `exp` claim and see if the token is still valid.|
|**Algorithm Confusion (None)**|Changing the algorithm to `"none"` can **bypass signature validation**.|Modify the token header to `"alg": "none"` and remove the signature.|
|**Insecure Storage of Tokens**|Storing tokens in **localStorage** can lead to **XSS-based token theft**.|Inject an **XSS payload** to steal tokens from `localStorage`.|
|**Token Replay Attacks**|If tokens lack **unique identifiers** or checks, they can be replayed.|Reuse a token after logout and check if access is still granted.|

---

### ✅ **Best Practices for Securing JWTs**

- **Use Strong Secrets:** Use **long, random keys** for HMAC algorithms.
- **Enforce Expiration:** Always set and validate the **`exp`** claim.
- **Use Asymmetric Encryption:** Prefer **RS256** over **HS256**.
- **Avoid `None` Algorithm:** Explicitly disable the **`none`** algorithm in libraries.
- **Token Storage:** Store tokens securely (e.g., **HTTP-only cookies**).
- **Token Rotation:** Implement **short-lived tokens** with refresh tokens.

---

### ✅ **Example of a Secure JWT (HS256)**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
.
{
  "sub": "1234567890",
  "name": "John Doe",
  "exp": 1714044800
}
.
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

✅ **Why Secure?**

- ✅ **Strong HMAC Key** used.
- ✅ **Expiration Claim (`exp`)** present.
- ✅ **No sensitive data** in the payload.

---

### 🔑 **Penetration Testing Checklist for JWTs:**

- [ ]  **Check for Weak Secrets:** Test with `John the Ripper`.
- [ ]  **Test Algorithm Switching:** Modify `"alg": "none"`.
- [ ]  **Replay the Token:** Attempt using the token after logout.
- [ ]  **Look for Missing Claims:** Ensure `exp` and `iat` are present.
- [ ]  **Inspect Storage Location:** Avoid tokens in `localStorage`.

---

### 🔗 **Final Takeaways:**

- **JWTs** are useful but must be **carefully implemented**.
- **Web App Pen Testers** can focus on **weak keys, missing claims, and algorithm misuse**.
- Tools like **jwt.io** simplify **token analysis** and **modification**.

✅ Let me know if you need **practice labs** or **hands-on examples** for JWT testing! 🚀