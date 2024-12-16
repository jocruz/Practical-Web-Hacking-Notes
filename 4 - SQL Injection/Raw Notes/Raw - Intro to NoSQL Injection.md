
---

## **_Introduction to NoSQL_**

NoSQL databases are designed to store, retrieve, and manage large volumes of data that do not fit well in traditional relational databases. They are known for their **flexibility**, **scalability**, and **high performance**.

### **Types of NoSQL Databases**

- **Document Stores**: MongoDB
- **Key-Value Stores**: Redis
- **Wide-Column Stores**: Cassandra
- **Graph Databases**: Neo4j

### **_Flexibility_**

NoSQL databases do not require a fixed schema, allowing the structure of the data to change over time.

### **_Scalability_**

Designed to scale out by using distributed architectures, making them suitable for cloud-based applications.

### **_Use Cases_**

- Big Data applications
- Real-time web apps
- IoT devices
- And more

---

An application uses **MongoDB** for its backend and retrieves user information based on user input **without proper input validation**.

---

## **Understanding NoSQL Injection: Bypassing Authentication**

### **Vulnerable Code**

```javascript
db.users.find({username: req.body.username, password: req.body.password});
```

- **What it does**:  
    The application queries the database to find a user where:
    - The `username` matches the input (`req.body.username`).
    - The `password` matches the input (`req.body.password`).

### **Bypassing Authentication**

To exploit this and bypass authentication, the attacker sends the following payload in the request body:

```json
{
  "username": "admin",
  "password": { "$ne": "" }
}
```

---

### **Why This Works**

1. **What the Payload Does**:
    
    - **`username`: "admin"`**: The attacker targets the` admin` user account.
    - **`password: { "$ne": "" }`**:
        - The `"$ne"` operator means **"not equal to"** in MongoDB.
        - This makes the query check for any `password` that **is not an empty string**.
2. **How the Query Changes**:  
    When the payload is injected, the query becomes:
    
    ```javascript
    db.users.find({username: "admin", password: { "$ne": "" }});
    ```
    
    - The database checks for a user where:
        - The `username` is `admin`.
        - The `password` is **not an empty string**.
3. **Why This is a Problem**:
    
    - Most passwords are not empty, so the condition `password != ""` is **true** for almost any user with a password.
    - MongoDB **accepts the payload** without validating it, so the query returns the `admin` user.

---

### **Step-by-Step Example of the Attack**

1. **Normal Login Attempt**:
    
    - A legitimate user submits:
        
        ```json
        {
          "username": "admin",
          "password": "wrongpassword"
        }
        ```
        
        This would fail if the password is incorrect.
2. **Injection Payload**:
    
    - The attacker sends:
        
        ```json
        {
          "username": "admin",
          "password": { "$ne": "" }
        }
        ```
        
    - The backend query now becomes:
        
        ```javascript
        db.users.find({username: "admin", password: { "$ne": "" }});
        ```
        
3. **What Happens**:
    
    - The `"$ne": ""` condition evaluates to true for any password that is **not empty**.
    - The database finds the `admin` user and logs the attacker in **without requiring the real password**.

---

### **Key Takeaways**

- **No Input Validation**: The code directly uses user input without checking its safety.
- **MongoDB Operators**: Special operators like `"$ne"` can be injected to bypass intended logic.
- **Outcome**: The attacker gains unauthorized access to the application.

---

