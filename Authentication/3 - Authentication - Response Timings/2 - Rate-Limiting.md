
## What is Rate Limiting?
- **Definition**: Rate limiting controls how many requests a user can send to an application in a given timeframe to prevent abuse, such as brute force attacks or Denial of Service (DoS).
- **Also Known As**: Throttling.

### Example
1. A login form tracks each login attempt by saving the user's **IP address**.
2. A counter increases with each attempt.
3. If more than **10 attempts** are made within **1 minute**, the IP is **blocked** temporarily.

---

## Checklist for Testing Rate Limiting
1. **Identify the Mechanism**:
   - What triggers the block? (IP, cookies, sessions?)
2. **Test Bypass Techniques**:
   - **Spoof Headers**: Replace or increment the following headers:
     - `X-Real-IP`
     - `X-Forwarded-For`
     - `X-Originating-IP`
     - `Client-IP`
     - `True-Client-IP`
   - **Rotate User Agents**: Change browser or device identifiers.
   - **Manipulate Cookies or Session Tokens**: Test if changing these resets the counter.
   - **Tamper HTTP Verbs**: Alternate verbs like `POST` or `GET`.
   - **Slow Down Attacks**: Reduce request frequency to mimic normal behavior.
   - **Simulate Legitimate Behavior**: Mimic realistic user patterns.

---

## Headers: What Do They Mean?

### Common Headers Used in Rate Limiting
1. **X-Real-IP**: Shows the original IP of the client (used by proxies like NGINX).
2. **X-Forwarded-For**: Lists all IPs the request passed through, starting with the client’s.
3. **X-Originating-IP**: Another way to denote the original client IP.
4. **Client-IP**: Some servers log the client’s IP using this header.
5. **True-Client-IP**: Used by some systems for accurate client identification.

### Mindset for Testing
- **Think Like an Attacker**:
  - Can I manipulate or spoof these headers to bypass rate limits?
  - Can I make the server think requests come from different users or IPs?
- **Test Incrementally**:
  - Focus on one bypass technique at a time to isolate weaknesses.
- **Be Patient**:
  - Rate limiting isn’t always immediate; slow, persistent attacks may succeed.

---

## Why Do Developers Implement Rate Limiting?

1. **Prevent Abuse**:
   - Stops brute force attacks, spamming, and excessive bot traffic.
2. **Maintain Performance**:
   - Prevents a single user from overloading the system.
3. **Enhance Security**:
   - Detects and blocks malicious behavior.

---

## Where is Rate Limiting Implemented?

1. **Backend (Server-Side)**:
   - Middleware (e.g., `express-rate-limit`) or custom logic in Node.js/Next.js APIs.
2. **Proxies and Load Balancers**:
   - Tools like NGINX or Cloudflare handle rate limiting at the edge before requests reach the server.
3. **Application Logic**:
   - In the backend code managing login, APIs, or sensitive forms.

---

## Brief History of Headers
- **X-Forwarded-For**: Introduced in the late 1990s by **Squid Proxy** to identify original client IPs.
- **X-Real-IP**: Popularized by **NGINX** for a simpler, single-IP solution.
- These headers became industry standards due to the need to track clients in modern web architectures.

---

## Related Topics
- [[Brute Force Attacks]]
- [[Burp Suite Notes]]
- [[Credential Stuffing]]

---

### **Observation Section**

---

#### **Key Takeaways**

1. **Header Manipulation**:
    
    - By modifying the `X-Forwarded-For` header, we were able to bypass lockout mechanisms, demonstrating how applications often rely on client-provided headers for user tracking and blocking.
    - This highlights the importance of sanitizing and verifying user-controlled inputs on the server side.
2. **Response Timing**:
    
    - Timing attacks can reveal sensitive information. In this lab, we identified valid usernames and passwords based on differences in response times, showcasing how application behavior can inadvertently leak information.
3. **Intruder Techniques**:
    
    - Using payload combinations with a **Pitchfork** attack allowed simultaneous testing of multiple variables (e.g., usernames and IP variations), increasing efficiency compared to other attack types.

---

#### **Why We Used a Pitchfork Attack**

- **Efficiency**: The Pitchfork attack enables pairing two payload sets to test combinations of inputs in a single request. This is ideal when:
    - One payload (e.g., `X-Forwarded-For` header modifications) influences the lockout mechanism.
    - Another payload (e.g., usernames or passwords) identifies valid credentials.
- **Contrast with Other Attacks**:
    - A **Sniper** attack tests one variable at a time, making it unsuitable for cases where multiple variables interact.
    - A **Cluster Bomb** attack generates all possible combinations of payloads, which is inefficient and unnecessary when the pairing of specific values suffices.

#### **How the Pitchfork Attack Works**

- In a Pitchfork attack, each payload position corresponds to a separate payload list. These lists are processed in parallel, pairing the first item from list 1 with the first item from list 2, the second with the second, and so on.
- This targeted pairing enables testing multiple dependent variables simultaneously without redundancy or excessive requests.

---

#responsetiming #ratelimit
