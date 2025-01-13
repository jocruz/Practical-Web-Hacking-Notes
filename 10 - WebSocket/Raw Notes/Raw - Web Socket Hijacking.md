Slide 1:

WebSocket Hijacking:

To Hijack a WebSocket we need to perform a CSRF (cross-site request forgery) attack on a WebSocket Handshake.

This is possible if:
1. The handshake relies on cookies
2. There are no CSRF tokens

Once established, we can send arbitrary messages to the application and retrieve information form the application

Slide2:
![[Pasted image 20250113015959.png]]
Websocket Hijacking

User -> 1. User authenticates & gets session token -> Server

User -> 2. User visits malicious site 

Server -> 3. Malicious site opens Websocket connection (includes users' cookies)

Site -> 4. Malicious site has read/write access to the WebSocket & sends data to the attacker

