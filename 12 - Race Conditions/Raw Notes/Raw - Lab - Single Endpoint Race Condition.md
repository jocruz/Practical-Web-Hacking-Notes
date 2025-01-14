
---

# Lab: Single-endpoint race conditions

Lab Description:

This lab's email change feature contains a race condition that enables you to associate an arbitrary email address with your account.

Someone with the address `carlos@ginandjuice.shop` has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.

To solve the lab:

1. Identify a race condition that lets you claim an arbitrary email address.
2. Change your email address to `carlos@ginandjuice.shop`.
3. Access the admin panel.
4. Delete the user `carlos`

You can log in to your own account with the following credentials: `wiener:peter`.

You also have access to an email client, where you can view all emails sent to `@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net` addresses.


Portswigger solution:

## Predict a potential collision

1. Log in and attempt to change your email to `anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net`. Observe that a confirmation email is sent to your intended new address, and you're prompted to click a link containing a unique token to confirm the change.
    
2. Complete the process and confirm that your email address has been updated on your account page.
    
3. Try submitting two different `@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net` email addresses in succession, then go to the email client.
    
4. Notice that if you try to use the first confirmation link you received, this is no longer valid. From this, you can infer that the website only stores one pending email address at a time. As submitting a new email address edits this entry in the database rather than appending to it, there is potential for a collision.
    

## Benchmark the behavior

1. Send the `POST /my-account/change-email` request to Repeater.
    
2. In Repeater, add the new tab to a group. For details on how to do this, see [Creating a new tab group](https://portswigger.net/burp/documentation/desktop/tools/repeater/groups#creating-a-new-tab-group).
    
3. Right-click the grouped tab, then select **Duplicate tab**. Create 19 duplicate tabs. The new tabs are automatically added to the group.
4. In each tab, modify the first part of the email address so that it is unique to each request, for example, `test1@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net, test2@..., test3@...` and so on.
    
5. Send the group of requests in sequence over separate connections. For details on how to do this, see [Sending requests in sequence](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-sequence).
    
6. Go back to the email client and observe that you have received a single confirmation email for each of the email change requests.
    

## Probe for clues

1. In Repeater, send the group of requests again, but this time in parallel, effectively attempting to change the pending email address to multiple different values at the same time. For details on how to do this, see [Sending requests in parallel](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-parallel).
    
2. Go to the email client and study the new set of confirmation emails you've received. Notice that, this time, the recipient address doesn't always match the pending new email address.
    
3. Consider that there may be a race window between when the website:
    
    1. Kicks off a task that eventually sends an email to the provided address.
    2. Retrieves data from the database and uses this to render the email template.
4. Deduce that when a parallel request changes the pending email address stored in the database during this window, this results in confirmation emails being sent to the wrong address.
    

## Prove the concept

1. In Repeater, create a new group containing two copies of the `POST /my-account/change-email` request.
    
2. Change the `email` parameter of one request to `anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net`.
    
3. Change the `email` parameter of the other request to `carlos@ginandjuice.shop`.
    
4. Send the requests in parallel.
    
5. Check your inbox:
    
    - If you received a confirmation email in which the address in the body matches your own address, resend the requests in parallel and try again.
    - If you received a confirmation email in which the address in the body is `carlos@ginandjuice.shop`, click the confirmation link to update your address accordingly.
6. Go to your account page and notice that you now see a link for accessing the admin panel.
    
7. Visit the admin panel and delete the user `carlos` to solve the lab.


Instructor solution:

Our victims address is `carlos@ginandjuice.shop`

From here we are going to log into our own credentials provided by the lab which is `wiener:peter`

Once we login we are going to go to My account, and from there we see that we can update our email, we are going to head over to the 'Email client' button to retrieve the email client to use to update our email and also receive email!

so our wiener email is 
#### wiener@exploit-0aca00130493b29e82f3af1001f6006f.exploit-server.net



From here we update our email address using the @ so

`john@exploit-0aca00130493b29e82f3af1001f6006f.exploit-server.net`

we click update email and we check the email client and we see that we get an email.

We head to Burp Suite to see the traffic in HTTP history and we see the following:

Method: Post
URL: /my-account/change-email

We see the Request body and we see it has an email parameter and a csrf parameter, we send this to repeater.

What we do is we change the email and add numbers just to keep track of what is happening

john1@...
john2@...
john3@...
john4@...
john5@...
john6@...
john7@...
john8@...
john9@...
john10@...

for each change we send each different email change parameter to repeater so we have 10 tabs, once again we click the + button, we create a new group tab, we name it Email Race Condition

and we send the group in parrallel

and we review the email client back in the lab, and we see that John9@... got an email for john5@...

so this proves we have an email race condition

The instructor then does the following:

We change the email parameter to test@... and another one to our victims email which is 
`carlos@ginandjuice.shop`

From here we group them together, name the group Carlos and we send them in parrellel again, but we spam the Send group (parallel) button to generate multiple traffic hoping we are in the race window

We go back to the email client and review our emails and we find that our test@... email got a email reset link for `carlos@ginandjuice.shop`, we click the link to confirm the password reset for `carlos@ginandjuice.shop` and we are brought back to the lab web app with confirmation that we got the email updated, we go back to the web app and refresh the page and we are logged in as carlos since the email shows this on the web app and we get an admin panel with the updated email, we go to the admin panel and delete the user carlos completing the lab!
