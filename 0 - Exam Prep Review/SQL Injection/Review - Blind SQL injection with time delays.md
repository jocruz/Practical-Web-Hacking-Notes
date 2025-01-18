
---

## **💡 Quick Overview**

This lab demonstrates **Blind SQL Injection using Time Delays**, where the response time of the application is used to infer the success of the attack. Both solutions exploit the `TrackingId` cookie by injecting a time delay payload (`pg_sleep(10)`) to confirm the vulnerability.

### **Why Both Solutions Work**:

- **Your Solution**: You tested the `TrackingId` cookie by appending the `pg_sleep(10)` function to introduce a 10-second delay. Observing the delayed response confirmed the vulnerability.
- **Instructor's Solution**: The instructor used a similar payload, focusing on systematic testing with different databases and detailed timing observations to ensure consistent delays.

Both approaches work because the database executes queries synchronously, and the injected delay function pauses execution, visibly affecting response times.

---

## **🛠️ Steps to Complete the Lab**

### **Your Solution**:

1. **Identify the Injection Point**:
    
    - Observed the application behavior and intercepted the `TrackingId` cookie in Burp Suite.
2. **Inject the Payload**:
    
    - Modified the cookie to include:
        
        ```plaintext
        TrackingId=x'||pg_sleep(10)--
        ```
        
    - Sent the request and observed a 10-second delay in the response, confirming the vulnerability.
3. **Complete the Lab**:
    
    - Observing a controlled 10-second delay fulfilled the lab’s objective.

---

### **Instructor's Solution**:

1. **Determine the Injection Point**:
    
    - Intercepted the `TrackingId` cookie in Burp Suite.
2. **Test Database-Specific Payloads**:
    
    - Injected payloads for time delay functions specific to common databases:
        - PostgreSQL:
            
            ```plaintext
            TrackingId=x'||pg_sleep(10)--
            ```
            
        - MySQL:
            
            ```plaintext
            TrackingId=x' AND SLEEP(10)--
            ```
            
        - Microsoft SQL Server:
            
            ```plaintext
            TrackingId=x'; WAITFOR DELAY '0:0:10'--
            ```
            
        - Oracle:
            
            ```plaintext
            TrackingId=x'||dbms_pipe.receive_message(('a'),10)--
            ```
            
3. **Confirm Successful Injection**:
    
    - Observed a consistent delay in the response (e.g., ~10 or ~20 seconds) to confirm the attack.
4. **Complete the Lab**:
    
    - The injection causing a controlled delay solved the lab.

---

## **❓ Why Time-Based SQL Injection Works**

Time-based SQL injection exploits **synchronous query execution** by injecting a function that delays the database's response. Here's why it works:

1. **Query Execution**:
    
    - When the server executes a SQL query, it waits for the database to complete the operation before responding.
    - By injecting a delay function (e.g., `pg_sleep(10)`), the server's response is intentionally slowed down.
2. **Confirmation of Vulnerability**:
    
    - If the injected query is executed successfully, the server’s delayed response confirms the injection worked.
3. **Database-Specific Functions**:
    
    - Different databases use different functions to introduce delays:
        - **PostgreSQL**: `pg_sleep(10)`
        - **MySQL**: `SLEEP(10)`
        - **SQL Server**: `WAITFOR DELAY '0:0:10'`
        - **Oracle**: `dbms_pipe.receive_message(('a'),10)`
4. **Impact**:
    
    - The delay allows attackers to infer conditions (e.g., whether a query succeeded) and proceed with further exploitation, such as extracting data.

---

## **❓ Why the Response Time May Vary**

1. **Double Delays**:
    
    - If the response time is longer than expected (e.g., 20 seconds instead of 10), it may indicate the application processes the injected value multiple times.
2. **Mitigation Checks**:
    
    - Delays stacking could result from security measures or redundant query executions.

---

## **Exam Readiness: Methodology for Time-Based SQL Injection**

1. **Identify Blindness**:
    
    - If no errors or visible indicators appear, consider time-based injection.
2. **Select Delay Functions**:
    
    - Use a cheat sheet to pick the correct function for the suspected database (e.g., `pg_sleep`, `SLEEP`).
3. **Test Payloads**:
    
    - Inject payloads like:
        
        ```plaintext
        '||pg_sleep(10)--
        ```
        
        and observe response times.
4. **Confirm Consistency**:
    
    - Resend requests to ensure the delay is consistent and verify the database type.
5. **Refine for Extraction**:
    
    - After confirming the injection, use time delays with conditional logic (e.g., `IF` statements) to extract data.

---

## **Key Takeaways**

- **Time-Based Inference**:
    
    - Blind SQL injection uses response delays as an indicator of success when no direct error messages or content differences are visible.
- **Database-Specific Functions**:
    
    - Knowing the right delay function for each database type is critical.
- **Confirmation of Vulnerability**:
    
    - Consistent delays in response times confirm successful injection.

---

Let me know if you’d like to refine this further! 😊