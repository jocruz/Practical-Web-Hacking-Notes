
---

### 📚 **JWT_Tool – Cracking and Manipulating JSON Web Tokens (JWTs)**

---

### 🚀 **High-Level Summary**

**JWT_Tool** is a powerful Python-based tool for testing the security of **JSON Web Tokens (JWTs)**. It allows **decoding**, **cracking**, **modifying**, and **exploiting** JWTs, focusing on **HMAC-SHA tokens**.

✅ **Purpose:**

- **Crack JWT secrets** when using weak HMAC keys.
- **Test for insecure JWT configurations** (e.g., weak secrets, missing expiration).
- **Tamper with JWT payloads** after secret discovery.

---

### 📦 **Definition Bank**

|**Term**|**Definition**|
|---|---|
|**JWT_Tool**|A Python tool for testing JWTs, including cracking weak HMAC secrets.|
|**HMAC-SHA256**|A **symmetric algorithm** where the same key is used for **signing** and **verifying** tokens.|
|**`--crack` Flag**|Attempts to **brute-force** the secret key used to sign the token using a **password list**.|
|**`-d` Flag (Dictionary)**|Specifies the **wordlist** used for the cracking attempt.|
|**Xato-1000 Wordlist**|A **password list** located in **`/usr/share/seclists`**, commonly used for brute-force attacks.|

---

### 📡 **Key Concepts and Structure of JWT_Tool Usage**

✅ **Tool Location (Instructor Setup):**

- The instructor installs **jwt_tool** in the **`/opt`** directory for easy access.

✅ **Basic Syntax:**

```bash
python3 jwt_tool.py [JWT] -flags
```

✅ **Example Command to Crack a JWT:**

```bash
python3 jwt_tool.py eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJsb2dpbiI6InRpY2FycGkifQ.aqNCvShlNT9jBFTPBpHDbt2gBB1MyHiisSDdp8SQvgw --crack -d /usr/share/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt
```

---

### 📦 **1. Cracking HMAC-SHA256 Tokens (`--crack` Flag)**

The **`--crack`** flag attempts to **brute-force** the secret key used to sign the JWT. It is specifically useful when the token uses a **weak HMAC key**.

✅ **Steps:**

1. The token is provided as the first argument.
2. **`--crack`** instructs the tool to attempt a brute-force attack.
3. **`-d [wordlist_path]`** specifies the dictionary used for cracking.

✅ **Example Output (Successful Crack):**

```
[*] Attempting to crack HMAC key...
[+] Key Found: cheese
```

---

### 📦 **2. Using the `-d` Flag (Dictionary Specification)**

The **`-d` flag** is used to specify a **password list** for brute-force attempts. The instructor specifically uses the **Xato-1000** list:

✅ **Wordlist Path (on Kali Linux VM):**

```plaintext
/usr/share/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt
```

✅ **What Makes This Wordlist Useful?**

- Contains **common weak passwords**.
- Ideal for **testing weak secrets** often used in **HMAC-SHA256 JWTs**.

✅ **Key Insight:**

- If the secret is found (like `"cheese"` in the example), the token can be **tampered with** and **re-signed** using the cracked key.

---

### 📡 **3. Modifying a Token After Cracking (JWT Tampering)**

✅ **Steps to Modify and Verify a Cracked JWT:**

1. **Crack the JWT secret** using the `--crack` flag.
2. Visit **[jwt.io](https://jwt.io/)** and paste the **JWT token**.
3. Modify the **payload** (e.g., change `"user": "regular"` → `"user": "Administrator"`).
4. Use the cracked secret (`cheese`) to **re-sign** the token under **HMAC-SHA256**.
5. If the secret was correctly cracked, the **JWT will be valid** after modification.

---

### 🎯 **Pen Testing Use Cases for JWT_Tool**

|**Use Case**|**Objective**|**How to Test**|
|---|---|---|
|**Weak Secret Cracking**|Test if the token uses a weak HMAC secret.|Run the `--crack` flag with a password list.|
|**Privilege Escalation**|Modify the token payload to gain admin privileges.|Crack the secret, modify payload, re-sign, test access.|
|**Replay Attacks**|Check if old tokens remain valid.|Reuse a cracked token with an expired `exp` claim.|
|**Token Forgery**|Forge a token without the server rejecting it.|Crack the HMAC key and create a new valid token.|

---

### ✅ **Best Practices for Preventing JWT Vulnerabilities**

- **Use Strong Secrets:** Ensure secrets are long and **cryptographically secure**.
- **Disable Symmetric Algorithms (HS256):** Use **asymmetric algorithms** like **RS256** where keys are harder to crack.
- **Implement Expiration (`exp` Claim):** Enforce token expiration and regular rotation.
- **Avoid Hardcoding Secrets:** Do not store secrets directly in the codebase.
- **Limit Token Scope:** Minimize the data stored in the JWT payload.

---

### ✅ **Summary of Commands:**

✅ **Installing JWT_Tool in Kali Linux:**

```bash
git clone https://github.com/ticarpi/jwt_tool.git /opt/jwt_tool
cd /opt/jwt_tool
pip3 install -r requirements.txt
```

✅ **Basic Crack Command:**

```bash
python3 jwt_tool.py [JWT] --crack -d [wordlist]
```

✅ **Example (with Kali Linux Wordlist):**

```bash
python3 jwt_tool.py eyJ0eXAiOiJK... --crack -d /usr/share/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt
```

✅ **JWT Manipulation (After Cracking):**

- Visit [jwt.io](https://jwt.io/).
- Paste the cracked JWT.
- Modify the payload (e.g., `"user": "Administrator"`).
- Re-sign the token with the cracked secret.

---

### 🔗 **Additional Resources:**

- 🔗 [JWT_Tool GitHub Repository](https://github.com/ticarpi/jwt_tool)
- 🔗 [JWT.IO Decoder & Validator](https://jwt.io/)

✅ **Final Takeaways:**

- **JWT_Tool** is effective for **brute-forcing** weak **HMAC secrets**.
- Use the **Xato-1000** list provided in Kali for testing weak keys.
- After cracking, **modification and privilege escalation** become possible.