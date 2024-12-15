IDOR - Insecure Direct Object Reference

Sometimes applications use user-supplied input to access objects directly.

- ﻿﻿Often used to access information of other objects (e.g. another user's account information)
- ﻿﻿May need to be combined with another weakness if the object ID is not easy to guess or brute-force
- ﻿﻿Can also impact files and work in various other contexts
- ﻿﻿Known as BOLA in APIs

```xml
<user>
	<id>123-124-125-126</id>
	<username>alex</username>
	<email>alex@alex.com</email>
</ user>
```

The XML code can be used to interpret this IDOR example, it can also be used for JSON objects. 

The idea is that we usually want to look for end points or data points in which we can attack. In this case we can try to brute force the ID, however if the id is too long we might want to look else where to either identify different complicated IDs and see if that works, for example, if there is a forum, and we can see the different comments, we can run the forum url through burp suite and be able to collect the different IDs and use them to test for IDOR. We can also try to attack the username and email to see if we get anything by brute forcing or doing the same thing looking elsewhere in the web application to see if we can use a correct username/email and testing IDOR in that direction. Depending on the web application we have to just take notes of the other securities that are in place when testing for IDOR.

Also to take note that depending on end points these end points might be leaking information and there for we are able to 

Moving On:
On another issue that is on the same level but different concept than IDOR is the following:


BAC (Broken Access Control)

/ BFLA (Broken Function Level Authorization

IDOR/BOLA is used when we access objects directly. BFLA or BAC is used when we can abuse functionality, such as updating the account of another user.

- **IDOR (Insecure Direct Object Reference)**: Allows access to objects (e.g., files, accounts) by manipulating references, like changing a URL parameter.
    
- **BOLA (Broken Object-Level Authorization)**: Broader category covering any failure to enforce authorization at the object level, including IDOR.
    
- **BAC (Broken Access Control)**: General term for failures in restricting access to resources or actions (includes IDOR/BOLA).
    
- **BFLA (Broken Function-Level Authorization)**: Specific to functions, where users access unauthorized actions or operations (e.g., admin functions).
    

Think of **BOLA/IDOR** as object-related and **BAC/BFLA** as access-related at different levels.

| **Category**            | **IDOR (Insecure Direct Object Reference)**          | **BOLA (Broken Object-Level Authorization)**      | **BAC (Broken Access Control)**                   | **BFLA (Broken Function-Level Authorization)**            |
| ----------------------- | ---------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------------- |
| **Similarities**        | Object-focused, involves unauthorized access to data | Object-focused, includes IDOR as a subset         | Both deal with authorization failures             | Both deal with function-level authorization failures      |
| **Main Difference**     | Exploits predictable or manipulatable object IDs     | Broader issue with enforcing object authorization | General: Includes all access control issues       | Specific: Deals with bypassing restrictions for functions |
| **Example Use Case**    | Accessing another user's account via ID (`id=123`)   | Viewing sensitive data without proper validation  | Accessing admin pages or data without permissions | Performing restricted actions like deleting user accounts |
| **Subset or Superset?** | Subset of BOLA                                       | Superset of IDOR                                  | Superset of BOLA and BFLA                         | Subset of BAC                                             |

#rawnotes #IDOR #accesscontrol 