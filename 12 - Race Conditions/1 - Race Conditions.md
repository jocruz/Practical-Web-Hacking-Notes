
---
## 📚 Race Conditions

### 🚀 High-Level Summary

A **race condition** occurs when multiple **concurrent requests** interact with the same resource, leading to **unexpected behavior**. Attackers exploit this by sending **parallel requests** during a **race window**, aiming to manipulate application logic, such as redeeming a coupon multiple times or bypassing security checks.

---

### 📦 Definition Bank

|**Term**|**Definition**|
|---|---|
|**Race Condition**|A flaw where multiple concurrent processes affect shared data, leading to unpredictable behavior.|
|**Race Window**|The **timeframe** between the check and the update of data where a collision can occur.|
|**Limit Overrun**|Exploiting a race window to **exceed the intended limit** (e.g., redeeming a coupon multiple times).|
|**Concurrency**|Multiple **processes or requests** interacting with the same data simultaneously.|
|**Time of Check, Time of Use (TOCTOU)**|A vulnerability where a **check** and its **use** happen separately, creating a race window.|

---

### 📊 How Race Conditions Work

A **race condition** occurs when a system processes **multiple simultaneous requests** without proper handling of **data synchronization**. Attackers can **manipulate** the timing to gain unauthorized actions.

**Steps:**

1. **User Action:** The user submits a POST request to **redeem a coupon**.
    
2. **Time of Check:** The server checks the **database** to verify coupon validity.
    
3. **Race Window Opens:** The server processes **multiple parallel requests** before the coupon is marked as **used**.
    
4. **Collision:** Each request **sees** the coupon as **valid** before the update.
    
5. **Time of Use:** Coupon is **redeemed multiple times**.
    
6. **Race Window Closes:** The server updates the coupon as **invalid** after multiple redemptions.


---

### 🖼️ Understanding the Race Condition Diagram

The following diagram illustrates the **race window** concept visually:

![[race condition.png]]

- The **race window** is the time between the **check** and **data update**.
    
- Multiple **concurrent requests** access the same coupon **before** it is invalidated.
    
- Some requests **succeed** because the coupon is marked as valid until the **first request** updates it.


---

### 🎯 Exploit Scenario: Coupon Code Abuse

**Example:**

```json
POST /redeem
{
    "coupon": "FREE100"
}
```

- An attacker sends **multiple parallel requests** using tools like **Burp Suite Intruder** before the coupon is marked as **used**.
    
- The coupon is **redeemed multiple times**, even though it was intended for **one-time use**.


---

### 🛠️ How to Identify Race Conditions

- **Observe Race Windows:** Look for **delays** between the **data check** and **update**.
    
- **Target:** Look for actions like **one-time coupon codes** or **bank transactions**.
    
- **Test with Parallel Requests:** Use tools like **Burp Suite Intruder** to send **simultaneous requests**.
    
- **Identify TOCTOU Issues:** Look for **separated validation and updates**.


---

### 🛡️ Preventing Race Conditions

- **Atomic Transactions:** Ensure that the **check** and **update** happen in a **single operation**.
    
- **Database Locking:** Use **row locks** to prevent multiple simultaneous actions on the same resource.
    
- **Rate Limiting:** Implement **request rate limits** to prevent excessive simultaneous requests.
    
- **Token-Based Control:** Use **one-time tokens** for critical actions like coupon redemptions.


---

### 🔧 Secure Code Example (Node.js)

```javascript
// Preventing Race Conditions with Database Locking
app.post('/redeem', async (req, res) => {
    const coupon = req.body.coupon;
    const result = await db.query('SELECT * FROM coupons WHERE code = ? FOR UPDATE', [coupon]);

    if (result.length > 0 && !result[0].used) {
        await db.query('UPDATE coupons SET used = true WHERE code = ?', [coupon]);
        res.send('Coupon successfully redeemed!');
    } else {
        res.status(400).send('Coupon invalid or already used');
    }
});
```

**Why Secure?**

- ✅ **Database Locking:** Prevents multiple redemptions of the same coupon.
    
- ✅ **Atomic Transaction:** Combines **check** and **update** in a **single step**.


---

### ✅ Key Takeaways

- **Race conditions** occur when **concurrent requests** interact with the same resource.
    
- **Exploitation** involves sending **parallel requests** to bypass restrictions.
    
- **Preventative measures** include **atomic operations**, **locking**, and **rate limiting**.
