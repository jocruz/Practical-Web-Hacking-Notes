# 🧪 Lab: Multi-endpoint Race Conditions

**Lab URL:** https://portswigger.net/web-security/race-conditions/lab-multi-endpoint

## 📖 Lab Description

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price. To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

✅ **Credentials:**

- Username: `wiener`
    
- Password: `peter`
    

---

## 🎯 **PortSwigger Solution Steps**

### 🕵️ **Predict a Potential Collision**

1. **Log in:** Use the provided credentials and purchase a gift card to examine the purchasing flow.
    
2. **Identify Key Endpoints:**
    
    - In Burp Proxy, examine the traffic and identify:
        
        - `POST /cart` (adds items to the cart)
            
        - `POST /cart/checkout` (submits the order)
            
3. **Examine Cart Management:**
    
    - Add a gift card to the cart and send a `GET /cart` request to Burp Repeater.
        
    - Test the request both **with and without** the session cookie.
        
    - Observe that without the cookie, the cart is empty, indicating:
        
        - The cart data is **server-side**.
            
        - Cart actions are likely keyed to the **session ID**.
            
4. **Identify Race Condition Potential:**
    
    - When submitting an order, validation and confirmation happen within the same request/response cycle.
        
    - A race condition could occur between order validation and confirmation.
        

---

### 📊 **Benchmark the Behavior**

1. **Send Requests to Repeater:**
    
    - Send both `POST /cart` and `POST /cart/checkout` to Burp Repeater.
        
2. **Group the Requests:**
    
    - Group both tabs together (Right-click → **Add to Group**).
        
3. **Test Sequential Requests:**
    
    - Send the requests **in sequence** using a single connection.
        
    - Notice a delay between the two requests.
        
4. **Simulate Network Delay:**
    
    - Add a `GET /` request for the homepage to the start of the group.
        
    - Send all three requests in sequence again to reduce the delay.
        
5. **Modify the Product ID:**
    
    - Change the `productId` parameter from `2` (gift card) to `1` (leather jacket).
        
6. **Send Sequential Requests:**
    
    - Send the modified requests again.
        
    - Observe that the order fails due to insufficient funds.
        

---

### ✅ **Prove the Concept**

1. **Prepare the Cart:**
    
    - Remove the leather jacket from the cart and add another gift card.
        
2. **Send Requests in Parallel:**
    
    - Send the `POST /cart` and `POST /cart/checkout` requests **in parallel**.
        
3. **Review the Responses:**
    
    - If the order is rejected:
        
        - Repeat the steps multiple times as race conditions are unpredictable.
            
    - If the response is **200 OK**, the leather jacket was purchased successfully, completing the lab!
        

---

## 🎓 **Instructor's Solution Walkthrough**

1. **Purchase a Gift Card:**
    
    - Log in using the credentials and add a gift card to the cart.
        
    - Complete the checkout process to reduce the store balance from **100 to 90**.
        
2. **Identify Key Requests:**
    
    - Observe the following requests in Burp Suite:
        
        - `POST /cart` with the body:
            
            ```
            productId=2&redirect=PRODUCT&quantity=1
            ```
            
        - `POST /cart/checkout` with a CSRF token.
            
3. **Send Requests to Repeater:**
    
    - Send both the `/cart` and `/cart/checkout` requests to Repeater.
        
4. **Modify the Payload:**
    
    - Change the `productId` from `2` (gift card) to `1` (leather jacket).
        
5. **Create a Repeater Group:**
    
    - Group the two requests in Repeater by selecting them and naming the group **Jacket**.
        
6. **Ensure the Cart is Ready:**
    
    - Add another gift card to the cart to ensure a product is present.
        
7. **Send Parallel Requests:**
    
    - Send the grouped requests **in parallel** using Burp's **Parallel Request Mode**.
        
8. **Check for Success:**
    
    - If the order fails due to insufficient funds, repeat the process until successful.
        

✅ **Key Takeaways from the Instructor:**

- **Identify Endpoints with a Limit:** Find an endpoint where a limit is enforced (`/cart/coupon`).
    
- **Use Parallel Requests:** Sending parallel requests can exploit race conditions effectively.
    

---

## 🧪 **Explanation of Race Condition and Payload**

### 🔑 **Why the Attack Works:**

- The server **validates** the cart contents and **checks the balance**.
    
- A **race condition** occurs when **two requests** are sent simultaneously, causing the server to complete both actions before fully validating the credit usage.
    

### 📦 **Payload Components:**

- `**POST /cart**`:
    
    ```
    productId=1&redirect=PRODUCT&quantity=1
    ```
    
    This adds the leather jacket to the cart.
    
- `**POST /cart/checkout**`:
    
    - Submits the checkout process.
        

✅ **By sending both requests together in parallel,** the server processes them before the balance is fully checked, completing the order despite insufficient funds.

---

## 🛠️ **Tools and Commands Used:**

- **Burp Suite:** Proxy, Repeater, Tab Grouping
    
- **Parallel Request Mode:** Used for race conditions.
    

---

## 🎯 **Key Takeaways:**

- Look for endpoints with **limit restrictions**.
    
- Race conditions rely on **parallel request execution**.
    
- Sending **multiple requests simultaneously** can bypass certain server-side checks.
    

✅ **Lab Completed Successfully!** 🎉