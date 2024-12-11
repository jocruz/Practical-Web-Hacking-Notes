What is Access Control?

Top 3 in the most important section of the course. It's easy to get Authentication and Authentication easily confused. Authentication is checking our identity, Authentication or Access control is what we are allowed to do. If we go to a hotel we are given a Hotel ID. This ID allows us to go to the gym, pool, sauna, it gives us the authorization to access/do certain things, and in that sense, it gives us Access Controls.  

Some of the types of Access Controls are Horizontal, some are Vertical.  Some are context dependent. So for example a Vertical Access Control will check if a user has privileges to access administrative functionality. We have different users with different level of access.

A horizontal access control will check if a user/object is allowed to access details of that account or that object, in that case this control allows things like users to see their own account details, but not the details of other users. So horizontal access control in this case.

The context dependent Access Control checks for things like if a user is allowed to do something based on the state of the application, for example, adding an item to a basket before the release date or when the stock is zero.


Also known as "Authorization"
In a nutshell: It's what you're allowed to do
Common finding in modern and complex application
Different types of access controls exist; Horizonal, vertical and context-dependent


So, these are the three different types of access controls that we have generally. Now, here are the typical issues that we run into, and they're all actually fairly easy to test for. So, forceful browsing, for example, browsing directly to something that we shouldn't be able to access. Now, we have idle, which is probably the most famous access control issue, so insecure direct object reference, and this allows us to manipulate the ID or the parameter that's used to access an object. So for example this could be the username or a user ID, and when we pass this in the URL, it says, hey, ID equals 56. If we change this to 57, we might access the information of the account that's tied to ID 57 rather than our own account, like 56. So, insecure direct object reference. When we're working with API's, sometimes, this is known as BOLA, Broken Object Level Authorization, and then if we can access functionality, like I mentioned before, so if standard user can update, a different user's account or if a user can use administrative functionality, we might call this BAFLA, Broken Function Level Authorization. An the main thing is, is that all of these attacks trust and use the user input supplied, so when we're accessing something, the application is saying, "Hey, I need to access this resource based on the user input" So once again, like the ID that we're supplying it or the username that we're supplying it, for example. So, now that we have an overview of access control issues, let's walk through a lab together before we take a closer look at some specific techniques and tools.

Typical Attacks

Typical Access Control Issues:
Forceful Browsing
IDOR/BOLA/BFLA
Trusting user input (E.g. headers, parameters)

Guided Lab: [https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter)

