
---

## Burp Suite Notes (Community Edition)

### Overview

**Burp Suite (Community Edition)** is a free web application security testing platform that provides essential manual testing features. It helps you intercept, modify, and analyze HTTP(S) requests and responses. While it lacks some advanced automation features of the paid version, the Community Edition still offers powerful tools like **Intruder**, **Repeater**, **Proxy**, **Decoder**, **Comparer**, and **Sequencer**, enabling manual security tests, credential stuffing attacks, timing analysis, and other research-oriented approaches.

### Key Tools & Features

#### 1. Proxy

- **Function**: Intercepts and inspects traffic between your browser and the target web application.
- **Usage**:
    - Configure your browser’s proxy settings to route traffic through Burp.
    - Modify requests/responses on the fly.
- **Tips**:
    - Perfect for understanding application behavior before moving to more complex attacks.

#### 2. Repeater

- **Function**: Manually modify and resend individual HTTP requests.
- **Usage**:
    - Right-click a captured request in Proxy and send it to Repeater.
    - Incrementally tweak parameters, headers, or body to see how the server responds.
- **Auto Scroll**:
    - Keep track of requests and responses as you send them repeatedly.
    - Useful for monitoring changes over multiple attempts without losing track.

#### 3. Intruder

**Intruder** is a powerful tool within Burp Suite for automating customized attacks on web parameters. While the Community Edition runs at a slower rate, it still supports these attack types:

- **Attack Types**:
    
    1. **Sniper**:
        - Tests one parameter at a time, cycling through a payload list.
        - Good for single-parameter fuzzing, flipping through different values (e.g., usernames).
    2. **Pitchfork**:
        - Tests multiple parameters in parallel, using synchronized payload lists by index.
        - Ideal for pairing user:pass lists or incrementing IPs and usernames simultaneously.
    3. **Cluster Bomb**:
        - Generates permutations from multiple payload sets across multiple parameters.
        - Useful if you need comprehensive coverage, e.g., testing all combinations of usernames and passwords.
- **Key Features**:
    
    - **Negative Search**:
        - Ability to filter out responses that contain certain strings (or conversely highlight responses that do not contain them).
        - Helpful for quickly identifying outliers in large sets of results.
    - **Sorting & Filtering**:
        - Sort requests by response length, status code, or response time to identify anomalies quickly.
    - **Manual Rate**:
        - Although the Community Edition lacks automated throttling, you can control when to start/stop attacks or use Intruder slowly to observe changes.

#### 4. Sequencer

- **Function**: Analyzes session tokens or other tokens for randomness and predictability.
- **Usage**:
    - Capture tokens from Proxy or Repeater, send to Sequencer.
    - Assess the token’s entropy and identify if session hijacking is feasible.

#### 5. Decoder

- **Function**: Encode/decode or hash strings.
- **Usage**:
    - Convert between formats (e.g., URL-encoding, Base64, HTML entities).
    - Useful for payload crafting and bypassing filters.

#### 6. Comparer

- **Function**: Compare two sets of data (e.g., responses, requests).
- **Usage**:
    - Identify differences between responses to subtle changes in parameters.
    - Great for spotting timing differences or unique indicators in responses.

### Actionable Methodologies

1. **Credential Stuffing / Bruteforce**:
    
    - Use Intruder (Sniper mode) to brute force usernames or passwords.
    - Employ Negative Search to filter out known failure messages.
    - Combine with IP spoofing headers (e.g., `[[HTTP Headers and Manipulation]]`) and timing attacks from `[[Username Enumeration via Response Timing]]`.
2. **Timing Attacks**:
    
    - Send repeated requests via Repeater or Intruder and measure response times.
    - Sort results to find delayed responses indicating valid usernames or parameters.
3. **Bypassing Filters**:
    
    - Use Decoder to try alternate encodings of payloads.
    - Use Repeater to test different headers and cookies until a bypass is found.

### Best Practices

- Always start with Proxy to understand baseline behavior.
- Use Repeater to refine and confirm vulnerabilities before scaling up to Intruder.
- Leverage Intruder’s attack modes based on testing goals:
    - **Sniper** for single parameter fuzzing.
    - **Pitchfork** for synchronous multi-parameter testing.
    - **Cluster Bomb** for comprehensive permutation attacks.
- Negative search and sorting capabilities help quickly identify useful results in large sets.
- Use Sequencer to test the randomness of tokens and identify session fixation or predictability issues.

### Common Pitfalls

- Running Intruder attacks blindly without sorting results or using negative search can lead to missed findings.
- Forgetting to use Decoder can cause you to overlook potential injection payloads.
- Ignoring session and token randomness can miss serious authentication flaws.

### Related Topics

- [[Youtube Script]]
- [[HTTP Headers and Manipulation]]

### Related Labs

- Labs that require brute forcing or token analysis will rely heavily on Burp’s Intruder and Repeater features.

### Tools and Commands

- Burp Suite Community Edition (no paid scanning features)
- [[Hydra Notes]] for brute forcing if needed outside Burp.

### Tags

#BurpSuite #tools #automation #analysis

### External Resources

- PortSwigger Web Security Academy tutorials on Burp features.
- OWASP Testing Guide for methodology references.

---

**Save this as `Burp Suite Notes.md` and link it to your existing notes.**