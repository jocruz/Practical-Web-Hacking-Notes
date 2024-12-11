
---
## Lab: Username Enumeration via Subtly Different Responses

## Challenge Lab: [Lab Username Enumeration via Subtly Different Responses](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses)


### Objective

Identify valid usernames and passwords by analyzing subtle differences in server responses to login attempts.

---

### **Steps to Solve**

1. **Analyzing Login Requests:**
    
    - Inspect the `POST /login` request.
    - Observe that the body contains parameters:
        
        ```
        username=test&password=test
        ```
        
2. **Setting Up Grep - Match in Burp Suite:**
    
    - Navigate to **Intruder > Options > Grep - Match**.
    - Add the string `"Invalid username or password."` from the response text to the list of match strings.
        - _Tip_: Copy the exact string from the response to avoid discrepancies.
    - **Warning**: Burp Suite may notify you about a live attack; proceed as it’s a safe lab environment.
3. **Filtering Results:**
    
    - Check the **Results** tab for requests where the response deviates from the typical `"Invalid username or password."`
    - Remove unnecessary columns from the **Results** tab in the **Grep - Match** settings, leaving only the column with your added match string.
4. **Identifying Valid Usernames:**
    
    - Look for a single request where the response **does not** match the string.
    - Note the username associated with this request (e.g., `accounts`).
5. **Testing Passwords:**
    
    - Place a **position marker** on the `password` field and configure the attack.
    - Observe response lengths in the **Results** tab:
        - Most responses will have a length of **3300+** characters.
        - Identify the exception (e.g., `1qaz2wsx`), which is the valid password.

---

### **Key Takeaways**

- Subtle differences in server responses (e.g., length or match strings) can indicate valid credentials.
- Burp Suite's **Intruder** and **Grep - Match** features allow targeted analysis of server responses.
- **Best Practice**: Always copy match strings directly from responses to avoid errors.

---

### **Alternative Approach**

- Use **ffuf** for rapid enumeration:
    - Pros: Faster.
    - Cons: Lacks detailed insight provided by Burp Suite Community Edition.

---

### **Tools and Commands**

- **Burp Suite Community Edition**
    - Intruder configuration: Position markers and Grep - Match filtering.
- **ffuf** (optional for faster enumeration).

---

### **Common Errors and Troubleshooting**

- **Error**: No deviation in response.
    - Solution: Double-check the match string in Grep - Match settings.
- **Error**: Invalid response lengths for password enumeration.
    - Solution: Ensure the position markers are correctly set in Intruder.

---

### **What to Do Next**

1. Practice similar enumeration labs to solidify the methodology.
    - [[2 - Lab - Brute Forcing Authentication]]
    - [[1 - Brute Force Attacks Overview]]
2. Experiment with **ffuf** for comparison with Burp Suite.
3. Study real-world cases where subtle server responses led to vulnerabilities.

---

### **Related Topics**

- [[Authentication Flaws]]
- [[1 - Brute Force Attacks Overview]]
- [[Burp Suite Notes]]
- [[Username Enumeration]]

---

### **Tags**

 #authentication #lab #brute-force 

---