
---
## 🔒 **High-Level Summary: Access Control**

Access Control, also known as **Authorization**, defines what users are allowed to do. While Authentication checks identity, Access Control ensures users can only perform specific actions or access certain resources they are authorized for.

---
## **Key Takeaways**

- **What is Access Control?**
    
    - **Access Control = Authorization**: Determines what you’re allowed to do.
    - Example: A hotel ID lets you access the gym or pool but restricts unauthorized areas.
    - Common in modern, complex applications.
      
- **Types of Access Controls**:
    
    - **Horizontal Access Control**: Ensures users can only access their own objects.
        - Example: A user can see their account details but not those of others.
    - **Vertical Access Control**: Ensures users have proper privileges for administrative or higher-level functions.
        - Example: A regular user cannot access admin functionality.
    - **Context-Dependent Access Control**: Based on application state.
        - Example: Prevents adding items to a cart before the release date or when out of stock.
          
- **Typical Issues**:
    
    - **Forceful Browsing**: Accessing restricted areas directly by manipulating URLs.
    - **IDOR (Insecure Direct Object Reference)**: Manipulating IDs or parameters to access unauthorized data.
        - Example: Changing `ID=56` in a URL to `ID=57` to access another user’s information.
    - **BOLA (Broken Object Level Authorization)**: Common in APIs, allowing unauthorized access to objects.
    - **BFLA (Broken Function Level Authorization)**: Users access higher privilege functionality.

---
## 📝 **Definition Bank**

| Term                          | Definition                                                              | Example                                                                       |
| ----------------------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Access Control**            | Defines what users are allowed to do (Authorization).                   | Using a hotel ID to access authorized areas like the gym or pool.             |
| **Horizontal Access Control** | Restricts users to their own objects.                                   | A user can view their account but not others'.                                |
| **Vertical Access Control**   | Restricts access based on privilege level.                              | A regular user cannot access admin-only functions.                            |
| **Context-Dependent Control** | Restricts access based on application state.                            | Preventing cart additions before the release date.                            |
| **IDOR**                      | Exploiting IDs/parameters to access unauthorized data.                  | Changing `ID=56` to `ID=57` to view another user’s details.                   |
| **BOLA**                      | Broken Object Level Authorization in APIs.                              | Accessing restricted API objects via improper authorization checks.           |
| **BFLA**                      | Broken Function Level Authorization, allowing higher privilege actions. | Standard user updates another user’s account or accesses admin functionality. |
| **Forceful Browsing**         | Accessing restricted areas by URL manipulation.                         | Entering a URL to directly access an admin dashboard without authorization.   |

---
## 🔒 **Advanced Notes on Access Control**

### **Overview**

- Access Control is **what you're allowed to do** (Authorization).
- Foundational to restricting actions and access in applications.

### **Types of Access Control**:

1. **Horizontal Access Control**: Ensures users can only access their objects or accounts.
    
    - Example: Viewing personal account details but not others’.
2. **Vertical Access Control**: Ensures users have correct privileges to perform functions.
    
    - Example: Admin privileges for higher-level tasks.
3. **Context-Dependent Access Control**: Allows or denies actions based on the state of the application.
    
    - Example: Adding to a cart only when items are in stock.

---
### **Typical Issues**

1. **Forceful Browsing**: Browsing directly to restricted areas without proper verification.
2. **IDOR (Insecure Direct Object Reference)**: Manipulating parameters like user IDs to access unauthorized objects.
    - Example: Changing `ID=56` to `ID=57` in a URL.
3. **BOLA (Broken Object Level Authorization)**: Exploiting API logic to access restricted objects.
4. **BFLA (Broken Function Level Authorization)**: Using higher-level functionalities without privilege.
5. **Trusting User Input**: Application uses input like headers or parameters without validation.

---
## 💡 **Examples of Access Control Vulnerabilities**

1. **IDOR/BOLA**:
    
    - Manipulating IDs or API calls to access another user's data.
    - Example: A user changes their user ID to view another user’s details.
2. **Forceful Browsing**:
    
    - Accessing restricted areas directly by entering URLs.
    - Example: Accessing `/admin/dashboard` without authentication.
3. **BFLA**:
    
    - Regular users gain access to admin functionality.
    - Example: Standard users updating other users' accounts.

---
## ✅ **Best Practices for Securing Access Control**

1. **Restrict Forceful Browsing**:
    
    - Validate access at every endpoint.
2. **Prevent IDOR/BOLA**:
    
    - Avoid exposing object identifiers in URLs.
    - Enforce proper authorization checks for every request.
      
3. **Secure Vertical Access**:
    
    - Enforce role-based access for sensitive functionality.
4. **Validate User Input**:
    
    - Never trust input like IDs, headers, or parameters without validation.

---
## 💭 **Thought-Provoking Questions**

1. How can you prevent IDOR in a complex web application?
2. What are the key differences between BOLA and IDOR?
3. How would you design context-dependent access control in an e-commerce application?

---
## 📘 **Additional Resources**

1. **Guided Lab**:
    
    - [Lab: User ID controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter)
2. **Articles**:
    
    - [OWASP Access Control Guidelines](https://owasp.org/www-project-cheat-sheets/cheatsheets/Access_Control_Cheat_Sheet.html)

---
### 🧠 **Checklist for Access Control Testing**

- **Understand Access Control Implementation**:
    - What types of controls are used? (Horizontal, Vertical, Context-Dependent)
- **Test Typical Issues**:
    - Forceful Browsing
    - IDOR/BOLA/BFLA
    - Trusting user input
- **Validate Authorization at Every Endpoint**:
    - Check for bypass techniques like ID tampering.
    - Enforce proper role-based restrictions.

---
### 🌟 **Memory Aid for Access Control Issues**

**FIT**:

- **F**orceful Browsing
- **I**DOR/BOLA
- **T**rusting User Input

---
