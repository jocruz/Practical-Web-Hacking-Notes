
## 💡 Quick Overview

The lab demonstrates a vulnerability in the 2FA implementation where the session cookie (`CookieA`) issued after logging in with a username and password remains valid for accessing sensitive pages, even without completing the 2FA step. By analyzing session cookies and directly navigating to `/my-account`, you exploit this mismanagement to bypass 2FA and access the victim's account.

## **🛠️ Lab Goal**

- **Objective**: Exploit improper session management to bypass 2FA and gain unauthorized access to a victim’s account.


	## **📌 Key Takeaways**
	
	1. **Session Cookie Mismanagement**: The application allows access to sensitive endpoints using `CookieA` from the first authentication step, without requiring 2FA validation.
	    
	2. **Authentication State Validation**: Properly implemented 2FA should invalidate partial authentication cookies (`CookieA`) and ensure all sensitive actions require full authentication.
	    
	3. **Testing Strategy**: Always test session cookies and attempt direct navigation to sensitive URLs to identify logical flaws in authentication mechanisms.
