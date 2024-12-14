Here are the updated notes for the **Lab: Insecure Direct Object References (IDOR)** with the screenshot integrated correctly and using **_key emphasis_** where applicable:

---

## **High-Level Summary of the Lab**

1. **Objective**: Exploit an **_Insecure Direct Object Reference (IDOR)_** vulnerability in the live chat transcript feature to retrieve another user's sensitive information (a password) and log into their account.
    
2. **What Makes This an IDOR?**:
    
    - **_IDOR_** occurs when an application exposes direct references to internal objects (e.g., files, database entries) without proper access controls.
    - In this lab, the transcripts are referenced by **_incrementing numbers_** in the file name (e.g., `2.txt`), allowing unauthorized users to **_enumerate and access other users' transcripts_**.
3. **Steps**:
    
    - Use the live chat feature to send a message and generate a transcript file.
    - Identify the **_URL structure for downloading transcripts_** (e.g., `/download-transcript/2.txt`).
    - Enumerate transcript files to locate **_sensitive information_** (e.g., a password).
    - Log in to the victim's account using the stolen credentials.
4. **Techniques Used**:
    
    - **_Parameter enumeration_** to exploit direct object references.
    - Analysis of **_HTTP requests and responses_** to identify accessible resources.
5. **Key Learning**:
    
    - Applications should implement **_proper access controls_** to restrict users from accessing resources that don’t belong to them.
    - **_IDs or file names_** used in URLs should not be **_easily predictable or sequential_**.

---

## **What to Do Next (Exam Simulation)**

- Always inspect **_URLs and filenames_** for patterns indicating sequential or predictable identifiers.
- Use Burp Suite Repeater or Intruder to automate **_enumeration and test for IDOR_** vulnerabilities.
- Log findings thoroughly, including **_sensitive information retrieved and how it was exploited_**.

---

## **Troubleshooting**

- **_If no valid transcripts are found_**:
    - Extend the range of numbers tested in Burp Suite Intruder.
    - Check for other possible patterns (e.g., non-numeric file names or IDs).
- **_If the password doesn’t work_**:
    - Verify that the username matches the account associated with the retrieved password.
    - Ensure there are no typos in the password during login.

---

## **Key Takeaways**

- Applications must validate **_user access to resources_** before serving them.
- **_Sequential or predictable object references_** (e.g., `2.txt`, `3.txt`) make applications vulnerable to **_IDOR_**.
- Burp Suite tools, such as Repeater and Intruder, are essential for **_efficiently testing and exploiting these vulnerabilities_**.

---

## **Step 1: Interact with the Live Chat**

1. Navigate to the lab's live chat feature and send a message:
    - Example: "Hello there."
2. After sending the message, select **_View Transcript_**.
3. Download the transcript file (e.g., `2.txt`).

---

## **Step 2: Investigate Transcript Access**

1. Note the **_URL structure for downloading transcripts_**:
    - Example: `/download-transcript/2.txt`.
2. Identify the pattern in the file names (e.g., **_sequential numbers_**).
3. Send the **_GET /download-transcript/2.txt_** request to Burp Repeater (Ctrl+R).

---

## **Step 3: Enumerate Transcript Files**

1. In Burp Suite Repeater, modify the file name in the request (e.g., change `2.txt` to `1.txt`).
2. If you find valid transcripts, review their contents for **_sensitive information_**.
3. To automate enumeration, send the request to **_Burp Intruder_**:
    - Set the positional marker on the file number in the URL (e.g., `/download-transcript/{2}.txt`).
    - Configure the payload type as **_Numbers_** and set the range (e.g., `0-50`).
4. Start the attack and analyze the responses:
    - **_200 Status Code_**: Valid transcript found.
    - **_400 Status Code_**: No transcript available.

---

### **_Screenshot of the Payload and Transcript_**

The screenshot below shows the **_Burp Intruder configuration_**, the payloads used, and the valid transcript response containing a password:

![[Pasted image 20241211090702.png]]

---

## **Step 4: Extract Sensitive Information**

1. Investigate the responses for **_200 status codes_**.
2. Locate the live chat transcript containing sensitive information:
    - Example: "The password for Carlos is **SuperSecure123**."

---

## **Step 5: Log In with Stolen Credentials**

1. Return to the main lab page.
2. Use the stolen credentials to log in:
    - **Username**: `Carlos`.
    - **Password**: _Password retrieved from the transcript_.
3. Navigate to Carlos’s account page to complete the lab.

---

## **Core Concept: Why This is an IDOR**

- The application directly exposes references to resources (chat transcripts) through **_predictable URLs_** without validating user permissions.
- By incrementing or modifying the file name (e.g., `2.txt` → `1.txt`), **_unauthorized access is granted_**.
- Proper access controls, such as **_session-based validation_** or **_cryptographically random file names_**, would mitigate this issue.

---

#IDOR #accesscontrol #websockets  #lab