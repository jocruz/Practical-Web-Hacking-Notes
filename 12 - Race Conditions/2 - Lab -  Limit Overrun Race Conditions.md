# 🧪 Lab: Limit Overrun Race Conditions

**Lab URL:**  
[https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability](https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability)

## 🎯 High-Level Summary

**Objective:** Exploit a race condition in the purchasing flow to buy the **Lightweight L33t Leather Jacket** for a significantly reduced price.

**Key Techniques & Concepts:**

- **Race Conditions:** Exploiting simultaneous requests to bypass restrictions.
    
- **Parallel Requests:** Sending multiple requests at once to trigger the race condition.
    
- **Coupon Exploitation:** Applying the same coupon multiple times.
    

**What Makes This Vulnerable?**

- The server fails to properly lock database transactions when a coupon is applied.
    
- This allows multiple discount applications if requests are processed simultaneously.
    

---

## ✅ PortSwigger Solution

### Predict a Potential Collision

1. **Log in:** Use the credentials `wiener:peter`.
    
2. **Buy a Cheap Item:** Add the cheapest item to the cart and apply the provided **PROMO20** discount code.
    
3. **Identify Interaction Endpoints:**
    
    - **POST /cart** (Adds items to the cart)
        
    - **POST /cart/coupon** (Applies the discount code)
        
4. **Key Observations:**
    
    - If the discount is applied more than once, the server responds with **Coupon already applied**.
        
    - The state of the cart is **server-side**, linked to the **session ID**.
        

### Benchmark the Behavior

1. Ensure no discount code is applied.
    
2. Send the `POST /cart/coupon` request to **Repeater**.
    
3. Group multiple tabs (around 20) for parallel testing.
    
4. Send the requests **sequentially**:
    
    - Only the first request succeeds.
        
    - The rest return **Coupon already applied**.
        

### Probe for Clues

1. Remove the discount code.
    
2. Send the **grouped requests in parallel**.
    
3. Observe multiple **Coupon applied** responses.
    
4. Refresh the cart and verify multiple discounts have been applied.
    

### Prove the Concept

1. Remove all items and discounts from the cart.
    
2. Add the **Lightweight L33t Leather Jacket** to the cart.
    
3. Send the `POST /cart/coupon` requests in parallel again.
    
4. Confirm the total price is significantly reduced.
    
5. Purchase the jacket to complete the lab.
    

---

## 📚 Instructor Solution

### Step 1: Setup and Initial Testing

- **Log in** using the provided credentials: `wiener:peter`.
    
- **Add an item to the cart.**
    
- Apply the **PROMO20** coupon.
    
- Verify the coupon is applied successfully.
    

### Step 2: Identifying the Vulnerable Endpoint

- Open **Burp Suite** and capture traffic.
    
- Locate the **POST /cart/coupon** request in the **HTTP History**.
    
- Observe that reapplying the coupon results in a **Coupon already applied** response.
    

### Step 3: Preparing for the Race Condition Test

- Remove the coupon from the cart.
    
- Send the **POST /cart/coupon** request to **Repeater**.
    
- Press **Ctrl+R** multiple times to create **15 Repeater Tabs**.
    

### Step 4: Grouping Requests

- Click the **+** icon in Repeater.
    
- Create a new **Tab Group** named `Race Condition`.
    
- Select all 15 tabs and group them together.
    

### Step 5: Executing the Race Condition Attack

- In **Repeater > Send**, choose **Single Group in Parallel (Single-Packet Attack)**.
    
- Send the group of requests simultaneously.
    
- Refresh the cart and observe multiple discounts applied.
    

### Step 6: Completing the Lab

- Place the order with multiple discounts applied.
    
- ✅ **Lab completed successfully!**
    

---

## 📖 Explanation of Payloads and Techniques

### **Why the Race Condition Works:**

- The server allows a brief time window where **multiple simultaneous discount applications** are processed before the state is updated.
    
- By sending requests in parallel, the race condition is triggered, resulting in multiple successful discounts.
    

### **Key Components Used:**

- **POST /cart/coupon**: The endpoint vulnerable to race conditions.
    
- **Parallel Request Sending:** Using Burp Suite's **Single Group in Parallel** feature to send requests simultaneously.
    

---

## 🛠️ Tools and Commands

- **Burp Suite:** Repeater, Tab Grouping, Parallel Request Sending
    
- **Key Request:**
    

```json
POST /cart/coupon
{
    "coupon": "PROMO20",
    "csrf": "<CSRF_TOKEN>"
}
```

---

## 🚩 Key Takeaways

- **Look for Restrictions:** Identify endpoints like `/cart/coupon` where actions are limited.
    
- **Parallel Testing:** Use tools like **Burp Suite** to send parallel requests.
    
- **Test Sequentially First:** Confirm behavior before moving to parallel attacks.
