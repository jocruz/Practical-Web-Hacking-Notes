# Multi-Endpoint Race Conditions 🏁

## Definition Bank

|**Term**|**Definition**|
|---|---|
|**Multi-Endpoint Race Condition**|A race condition where multiple endpoints are involved in the vulnerable process.|
|**Race Window**|The period between checking a condition and using that data where a collision can occur.|
|**Atomic Transactions**|A process where operations are completed as a single, indivisible step.|
|**Third-Party Payment Gateway**|An external service used to handle payment processing separate from the main app.|
|**Concurrency**|The simultaneous execution of multiple operations or threads in a system.|

---

## 🎯 High-Level Summary

A **multi-endpoint race condition** occurs when multiple endpoints process a sequence of actions simultaneously, introducing the risk of manipulating business logic. This vulnerability can be exploited when data is altered **between validation and completion** steps across different requests.

---

## How Multi-Endpoint Race Conditions Work

### Example: E-Commerce Checkout Manipulation

1. **Step 1:** User checks out, and the server validates the basket's contents (3 items for $99).
2. **Step 2:** The server sends a payment request to a third-party payment provider.
3. **Step 3:** The payment is validated, and the basket contents are finalized in the order.

### **Exploit:**

If the attacker adds **extra items** after the basket validation but **before order completion**, additional items may be added **without being charged** if no further validation occurs.

---

## 🖼️ Visual Explanation

![[Pasted image 20250113180645.png]]

### **Explaining the Image:**

- **Testing Multiple Endpoints:** Several requests interacting with the same data flow.
- **Example Scenario:** Adding more items to a basket during the checkout process before the final payment confirmation.
- **Race Window:** The time between **basket validation** and **order completion**, where the attacker can modify the order.

---

## Methodology for Testing Multi-Endpoint Race Conditions

1. **Test the Application Flow:**
    - Identify steps where multiple endpoints interact with shared data (e.g., checkout and payment validation).
2. **Analyze the Logic:**
    - Check whether the server revalidates the data before finalizing a transaction.
3. **Send Parallel Requests:**
    - Use tools like **Burp Suite Turbo Intruder** to send multiple requests during the race window.
4. **Observe Impact:**
    - Verify if unvalidated actions (like adding extra items) are processed.
5. **Confirm Exploitability:**
    - Test repeatedly, as timing attacks require precision.

---

## 🎯 Exploitation Scenario

### Objective: Bypass Payment Verification

- **Step 1:** Add 3 items to a basket.
- **Step 2:** Confirm basket and begin payment validation.
- **Step 3:** During validation, send a **parallel request** to add more items to the basket.
- **Step 4:** If the order completes without revalidating, the attacker gets the **extra items for free**.

---

## 🛡️ Mitigation Strategies

- **Atomic Transactions:** Ensure all steps complete together or fail together.
- **Concurrency Control:** Implement data locks to prevent simultaneous updates.
- **Revalidate Inputs:** Always recheck basket contents before finalizing a transaction.
- **Rate Limiting:** Prevent excessive simultaneous requests.
- **Testing:** Use **Burp Suite** to simulate multiple requests during the race window.

---

✅ **Summary:**  
**Multi-Endpoint Race Conditions** occur when different endpoints process data asynchronously, allowing attackers to manipulate the process. **Race windows** exist between **data validation** and **finalization**, creating an opportunity for exploitation. Proper **locking mechanisms**, **data validation**, and **atomic transactions** can mitigate this risk.