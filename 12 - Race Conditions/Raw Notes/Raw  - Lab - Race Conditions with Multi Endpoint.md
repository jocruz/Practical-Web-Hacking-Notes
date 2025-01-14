# Lab: Multi-endpoint race conditions


Lab Description:

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

You can log into your account with the following credentials: `wiener:peter`.

Port Swigger solution:
## Predict a potential collision

1. Log in and purchase a gift card so you can study the purchasing flow.
    
2. Consider that the shopping cart mechanism and, in particular, the restrictions that determine what you are allowed to order, are worth trying to bypass.
    
3. From the proxy history, identify all endpoints that enable you to interact with the cart. For example, a `POST /cart` request adds items to the cart and a `POST /cart/checkout` request submits your order.
    
4. Add another gift card to your cart, then send the `GET /cart` request to Burp Repeater.
    
5. In Repeater, try sending the `GET /cart` request both with and without your session cookie. Confirm that without the session cookie, you can only access an empty cart. From this, you can infer that:
    
    - The state of the cart is stored server-side in your session.
    - Any operations on the cart are keyed on your session ID or the associated user ID.
    
    This indicates that there is potential for a collision.
    
6. Notice that submitting and receiving confirmation of a successful order takes place over a single request/response cycle.
    
7. Consider that there may be a race window between when your order is validated and when it is confirmed. This could enable you to add more items to the order after the server checks whether you have enough store credit.
    

## Benchmark the behavior

1. Send both the `POST /cart` and `POST /cart/checkout` request to Burp Repeater.
    
2. In Repeater, add the two tabs to a new group. For details on how to do this, see [Creating a new tab group](https://portswigger.net/burp/documentation/desktop/tools/repeater/groups#creating-a-new-tab-group)
    
3. Send the two requests in sequence over a single connection a few times. Notice from the response times that the first request consistently takes significantly longer than the second one. For details on how to do this, see [Sending requests in sequence](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-sequence).
    
4. Add a `GET` request for the homepage to the start of your tab group.
    
5. Send all three requests in sequence over a single connection. Observe that the first request still takes longer, but by "warming" the connection in this way, the second and third requests are now completed within a much smaller window.
    
6. Deduce that this delay is caused by the back-end network architecture rather than the respective processing time of the each endpoint. Therefore, it is not likely to interfere with your attack.
    
7. Remove the `GET` request for the homepage from your tab group.
    
8. Make sure you have a single gift card in your cart.
    
9. In Repeater, modify the `POST /cart` request in your tab group so that the `productId` parameter is set to `1`, that is, the ID of the **Lightweight L33t Leather Jacket**.
    
10. Send the requests in sequence again.
    
11. Observe that the order is rejected due to insufficient funds, as you would expect.
    

## Prove the concept

1. Remove the jacket from your cart and add another gift card.
    
2. In Repeater, try sending the requests again, but this time in parallel. For details on how to do this, see [Sending requests in parallel](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-parallel).
    
3. Look at the response to the `POST /cart/checkout` request:
    
    - If you received the same "insufficient funds" response, remove the jacket from your cart and repeat the attack. This may take several attempts.
    - If you received a 200 response, check whether you successfully purchased the leather jacket. If so, the lab is solved.


Instructor solution:

We investigate the lab web app, and we add a gift card to our check out, we then complete the check out and our balance goes from 100 to 90 because the gift card was only 10 credits.

This was done to generate traffic and from here we look through our traffic and see we have 2 end points that have information that we might be able to use

The first one is /cart and in the request body is

productId=2&redirect=PRODUCT&quantity=1

from here we can go to another POST request called /cart/checkout and look at the body and we see a CSRF token, but the request seems like it sends a post to the end point and thats it

so we send both to repeater.

so we modify the productID from 2 to 1, and we do this because the leather jacket was the first in the item in the web page and the gift card was the second.

We group them together in repeater, we name the group Jacket

Once we group them together we send it in parallels, but before we do, we have to make sure we have something added to our cart for the request to go through, so we add the giftcard into our basket once again,

go back to repeater, and then send the request in parallels.

if it fails, we can just try again with the same steps and we solve the lab!