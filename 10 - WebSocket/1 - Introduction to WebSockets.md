### 📚 WebSockets Overview

---

### 🚀 High-Level Summary

**WebSockets** provide a **full-duplex**, **real-time** communication protocol that allows **bidirectional** data transfer between a **web browser** and a **web server** over a **single TCP connection**. They are ideal for **live chats, gaming, and notifications** due to their **low latency** and **persistent connection**.

---

### 📦 Definition Bank

|**Term**|**Definition**|
|---|---|
|**WebSocket**|A protocol enabling **real-time, bidirectional communication** over a TCP connection.|
|**Full-Duplex**|Both **client** and **server** can send and receive messages **simultaneously**.|
|**WebSocket Handshake**|The **initial connection** upgrade from HTTP to WebSockets.|
|**TCP Connection**|A persistent **two-way** communication channel.|
|**WebSocket Frame**|A **data packet** exchanged between client and server.|

---

### 📡 How WebSockets Work (Simplified)

1. **Client Initiates:** The browser sends a **WebSocket handshake** (`Upgrade: websocket`).
2. **Server Responds:** The server **accepts** with `101 Switching Protocols`.
3. **Persistent Connection:** Data is exchanged **continuously** until the connection is closed.

---

### 🧩 WebSocket Example (JavaScript)

```javascript
const socket = new WebSocket('wss://example.com/socket');

// Send data to the server
socket.onopen = () => socket.send('Hello Server!');

// Receive data from the server
socket.onmessage = (event) => console.log('Server:', event.data);

// Close the connection
socket.onclose = () => console.log('Connection closed');
```

---

### 📊 WebSocket vs HTTP

|**Feature**|**WebSockets** ✅|**HTTP** ❌|
|---|---|---|
|**Communication**|Full-Duplex (Two-way)|Half-Duplex (One-way per request)|
|**Connection Type**|Persistent (Open until closed)|Stateless (New request per action)|
|**Latency**|Very Low|Higher due to repeated handshakes|
|**Use Case**|Real-Time Apps (Chat, Games)|Static Content, REST APIs|

---

### 🛠️ How to Identify WebSockets (Pen Testing Focus)

- **DevTools:** Check the **Network Tab > WS** for open WebSocket connections.
- **JavaScript Inspection:** Search for `new WebSocket()` instances.
- **Headers:** Look for the **`Upgrade: websocket`** header.

---

### 🕵️‍♂️ WebSocket Security Issues to Test For

- **Authentication Bypass:** Ensure **token validation** occurs on every request.
- **Input Validation:** Test for **XSS** by injecting payloads into messages.
- **Rate Limiting:** Verify if excessive connections can **flood** the server.
- **Session Handling:** Confirm if **WebSockets close** after a session expires.

---

### 🛡️ WebSocket Security Best Practices

- **Validate Input:** Always **sanitize** incoming data.
- **Use Secure WebSockets:** Prefer **`wss://`** (encrypted).
- **Authenticate Every Message:** Use **JWT tokens** or **API keys**.
- **Implement Rate Limiting:** Prevent **DoS attacks**.
- **Restrict Origins:** Enforce **CORS policies** for trusted domains only.

---

### ✅ Key Takeaways

- **WebSockets** are perfect for **real-time** data exchange.
- Use **secure handling** (`wss://`) and **validate all input**.
- Test for **authentication issues**, **input validation**, and **rate limiting**.