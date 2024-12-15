
## 🔒 **High-Level Summary: Blind SQL Injection (SQLi)**

**_Blind SQL Injection_** occurs when the target application is vulnerable to SQL injection, but the **_query results are not directly visible_** in the response. Instead, attackers rely on indirect methods like **_conditional responses, delays, or errors_** to extract information.

---

## 📝 **Definition Bank**

| **Term**                       | **Definition**                                                                                            |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| **Blind SQL Injection (SQLi)** | SQL injection where query results are not returned directly, requiring alternative methods to infer data. |
| **Boolean-Based SQLi**         | A technique that uses **true or false conditions** to infer information based on application responses.   |
| **Time-Based SQLi**            | A technique that uses **delays** (e.g., `SLEEP(10)`) to infer information based on response time.         |
| **Error-Based SQLi**           | A technique that triggers database errors to reveal data indirectly.                                      |
| **Conditional Responses**      | Observing how the application reacts to different queries (e.g., adding or removing data).                |
| **Payloads**                   | Predefined SQL queries used to exploit vulnerabilities and extract information.                           |

---

## 🔍 **Understanding SQLi Payloads**

### **SQL Query for the Code Above**

**Test Data Setup**:

- `users` table includes usernames, passwords, and roles.
- `products` table stores product details (e.g., name, description, price).

---

### **Example Payloads and Their Purpose**

1. **Boolean-Based SQLi**:
    - **Payload**: `' AND 1=1-- -`
        - **Why it works:** The condition `1=1` is always true, so the application responds as if the query succeeded.
        - **Effect:** Confirms if SQL injection is possible.
    - **Payload**: `' AND 1=2-- -`
        - **Why it works:** The condition `1=2` is false, so the application response changes.
        - **Effect:** Differentiates between true/false responses, proving SQL injection is present.

---

2. **Time-Based SQLi**:
    - **Payload**: `SELECT SLEEP(10)`
        - **Why it works:** Forces the database to pause for 10 seconds if the condition is true.
        - **Effect:** A delayed response confirms that the injection is successful.
    - **Example with conditions**:
        
        ```sql
        SELECT IF(SUBSTRING((SELECT database()),1,1)='a', SLEEP(5), 'a');
        ```
        
        - **Explanation:** If the first character of the database name is `'a'`, the query sleeps for 5 seconds.

---

3. **Error-Based SQLi**:
    - **Payload**:
        
        ```sql
        ' OR (SELECT CASE WHEN (ASCII(SUBSTRING((SELECT database()),1,1)) = 97) THEN CAST('' AS INT) ELSE '' END)-- '
        ```
        
        - **Why it works:** Forces an error if the condition is true, revealing data indirectly.
        - **Effect:** Character-by-character extraction of the database name or table names.

---

### **Key Observations**

- **Boolean-Based**: Relies on how the application reacts to **true/false** queries.
- **Time-Based**: Uses response time as a clue for success.
- **Error-Based**: Leverages database errors to infer data.

---

## 🔒 **Scripting Solutions for Blind SQLi**

### **Tools vs. Custom Scripts**

1. **Tools**:
    
    - **Advantages:** Faster workflows, automation of common payloads (e.g., SQLmap).
    - **Disadvantage:** Less precise, harder to customize.
2. **Custom Scripts**:
    
    - **Advantages:** Tailored to specific applications, better for learning and understanding attack methods.
    - **Disadvantage:** Slower and less efficient in time-sensitive scenarios.

**_Recommendation:_** Use tools for common cases and custom scripts for edge cases or when understanding the attack in depth is critical.

---

## 📝 **Blind SQL Injection Testing Methodology**

1. **Start with Boolean-Based Payloads**:
    
    - Use payloads like `' AND 1=1-- -` and `' AND 1=2-- -` to test for true/false responses.
    - Observe how the application reacts (e.g., changes in results or behavior).
2. **Test Time-Based Payloads**:
    
    - Introduce delays (e.g., `SLEEP(10)`) to identify if the database executes the query.
    - Example:
        
        ```sql
        SELECT IF(1=1, SLEEP(5), 'a');
        ```
        
3. **Use Error-Based Payloads**:
    
    - Trigger errors to reveal information.
    - Example:
        
        ```sql
        SELECT CASE WHEN (SUBSTRING((SELECT database()),1,1)='a') THEN CAST('' AS INT) ELSE '' END;
        ```
        
4. **Iterate to Extract Data**:
    
    - Adjust payloads to extract data character-by-character:
        - **Database Name:** `SUBSTRING((SELECT database()), 1, 1)`
        - **Table Names:** `SELECT table_name FROM information_schema.tables`.

---

## 🔍 **Understanding SQLi Payloads**

### **Link to SQL Playground**:

[https://www.db-fiddle.com/f/nLpyQDMd49iRygnY9H7CB8/5](https://www.db-fiddle.com/f/nLpyQDMd49iRygnY9H7CB8/5)

The following examples use the database schema provided in the link above, which consists of a `users` table (storing usernames, passwords, and roles) and a `products` table (storing product details).

---

### **Schema Overview**

**Users Table:**

```sql
CREATE TABLE `users` (
  `id` int NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL,
  `password` varchar(50) NOT NULL,
  `role` varchar(20) NOT NULL,
  PRIMARY KEY (`id`)
);
```

Sample data:

|id|username|password|role|
|---|---|---|---|
|1|Jessamy|password123|admin|
|2|Jeremy|qwerty|user|

---

**Products Table:**

```sql
CREATE TABLE `products` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(100) NOT NULL,
  `description` text,
  `price` decimal(10, 2) NOT NULL,
  PRIMARY KEY (`id`)
);
```

Sample data:

|id|name|description|price|
|---|---|---|---|
|1|Laptop|15-inch laptop with 8GB RAM|1200.00|
|2|Smartphone|Latest model with 5.7-inch screen|299.99|
|3|Coffee Maker|Brews coffee and has a timer|49.99|

---

### **Payloads and Explanations**

#### **Simple SQL Statements**

1. **`SELECT * FROM users;`**
    
    - **What it does:** Retrieves all rows from the `users` table, including usernames, passwords, and roles.
    - **Use case:** Understand the structure and data within the `users` table.
2. **`SELECT * FROM users WHERE username = 'Jessamy';`**
    
    - **What it does:** Retrieves details about the user `Jessamy` (e.g., `password` and `role`).
    - **Why it’s useful:** Filters user-specific data for further exploitation.
3. **`SELECT * FROM users WHERE username = 'Jessamy' AND password = 'password123';`**
    
    - **What it does:** Validates if the provided username and password are correct.
    - **Why it’s useful:** Used for bypassing authentication.
4. **`SELECT * FROM users WHERE username = 'Jessamy'-- ' AND password = 'password123';`**
    
    - **What it does:** The `--` starts a comment, ignoring everything after it.
        - The query becomes:
            
            ```sql
            SELECT * FROM users WHERE username = 'Jessamy';
            ```
            
    - **Effect:** Bypasses the password check, potentially allowing unauthorized access.

---

#### **Substring Extraction**

1. **`SELECT database();`**
    
    - **What it does:** Returns the name of the current database.
    - **Why it’s useful:** Helps attackers understand the database context.
2. **`SELECT SUBSTRING(database(), 1, 1);`**
    
    - **What it does:** Extracts the first character of the database name.
    - **Why it’s useful:** Enables attackers to enumerate the database name character by character in blind SQLi scenarios.

---

#### **Boolean-Based Testing**

1. **`SELECT * FROM products WHERE name = 'Laptop' AND 1=1;`**
    
    - **What it does:** The condition `1=1` is always true, so the query behaves normally.
    - **Why it’s useful:** Confirms SQL injection if the response is unchanged.
2. **`SELECT * FROM products WHERE name = 'Laptop' AND 1=2;`**
    
    - **What it does:** The condition `1=2` is always false, so the query returns no rows.
    - **Why it’s useful:** Helps identify SQL injection vulnerabilities by observing changes in the application's response.

---

#### **Time-Based Testing**

1. **`SELECT * FROM products WHERE name = 'Laptop' AND SLEEP(5);`**
    
    - **What it does:** If the query executes successfully, the database delays its response by 5 seconds.
    - **Why it’s useful:** Confirms SQL injection vulnerabilities when the response is delayed.
2. **Time-Delay Substring Example:**
    
    ```sql
    SELECT IF(SUBSTRING((SELECT database()), 1, 1) = 'a', SLEEP(5), 'a');
    ```
    
    - **What it does:** Checks if the first character of the database name is `'a'`. If true, the query sleeps for 5 seconds.
    - **Effect:** Character-by-character database name extraction in blind SQLi.

---

#### **Error-Based Testing**

1. **`SELECT IF(SUBSTRING((SELECT database()), 1, 1)='a', (SELECT table_name FROM information_schema.tables), 'a');`**
    
    - **What it does:** Extracts table names from the database schema if the condition is true.
    - **Why it’s useful:** Leverages errors to extract metadata about the database.
2. **Error-Based Character Matching:**
    
    ```sql
    SELECT CASE WHEN (ASCII(SUBSTRING((SELECT database()), 1, 1)) = 97) THEN CAST('' AS INT) ELSE '' END;
    ```
    
    - **What it does:** Forces a database error if the first character matches the ASCII value of `'a'` (97).
    - **Why it’s useful:** Helps extract data in blind SQLi scenarios by triggering errors.

---

## ✅ **Actionable Insights**

1. **_Boolean-Based Testing:_**
    
    - Use true/false conditions to confirm vulnerabilities by observing response changes.
2. **_Time-Based Testing:_**
    
    - Leverage delays to confirm vulnerabilities without visible output (e.g., blind SQLi).
3. **_Error-Based Testing:_**
    
    - Extract information by forcing database errors and interpreting their output.
4. **_Iterate Character by Character:_**
    
    - Use payloads like `SUBSTRING` and `ASCII` to extract sensitive data incrementally.

---

## 💼 **Possible Interview Questions**

1. **How does the `' AND 1=1-- -` payload confirm SQL injection?**
    
    - **Answer:** _It creates a condition that always evaluates to true. If the response remains unchanged, it indicates SQL injection._
2. **How can time delays help identify blind SQL injection?**
    
    - **Answer:** _Delays like `SLEEP(5)` confirm SQLi by introducing noticeable response delays if the condition is true._
3. **What is the purpose of error-based SQLi payloads?**
    
    - **Answer:** _To leverage database errors to extract sensitive information in the absence of direct query results._
4. **How does `SUBSTRING` assist in blind SQL injection?**
    
    - **Answer:** _It allows incremental extraction of data (e.g., database names or table names) by isolating specific characters._

---