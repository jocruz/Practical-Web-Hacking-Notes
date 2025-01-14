# Lab: Limit overrun race conditions

Lab description:
This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

You can log in to your account with the following credentials: `wiener:peter`.


### Portswigger Solution

## Predict a potential collision

1. Log in and buy the cheapest item possible, making sure to use the provided discount code so that you can study the purchasing flow.
    
2. Consider that the shopping cart mechanism and, in particular, the restrictions that determine what you are allowed to order, are worth trying to bypass.
    
3. In Burp, from the proxy history, identify all endpoints that enable you to interact with the cart. For example, a `POST /cart` request adds items to the cart and a `POST /cart/coupon` request applies the discount code.
    
4. Try to identify any restrictions that are in place on these endpoints. For example, observe that if you try applying the discount code more than once, you receive a `Coupon already applied` response.
    
5. Make sure you have an item to your cart, then send the `GET /cart` request to Burp Repeater.
    
6. In Repeater, try sending the `GET /cart` request both with and without your session cookie. Confirm that without the session cookie, you can only access an empty cart. From this, you can infer that:
    
    - The state of the cart is stored server-side in your session.
    - Any operations on the cart are keyed on your session ID or the associated user ID.
    
    This indicates that there is potential for a collision.
    
7. Consider that there may be a race window between when you first apply a discount code and when the database is updated to reflect that you've done this already.
    

## Benchmark the behavior

1. Make sure there is no discount code currently applied to your cart.
    
2. Send the request for applying the discount code (`POST /cart/coupon`) to Repeater.
    
3. In Repeater, add the new tab to a group. For details on how to do this, see [Creating a new tab group](https://portswigger.net/burp/documentation/desktop/tools/repeater/groups#creating-a-new-tab-group).
    
4. Right-click the grouped tab, then select **Duplicate tab**. Create 19 duplicate tabs. The new tabs are automatically added to the group.
5. Send the group of requests in sequence, using separate connections to reduce the chance of interference. For details on how to do this, see [Sending requests in sequence](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-sequence).
    
6. Observe that the first response confirms that the discount was successfully applied, but the rest of the responses consistently reject the code with the same **Coupon already applied** message.
    

## Probe for clues

1. Remove the discount code from your cart.
    
2. In Repeater, send the group of requests again, but this time in parallel, effectively applying the discount code multiple times at once. For details on how to do this, see [Sending requests in parallel](https://portswigger.net/burp/documentation/desktop/tools/repeater/send-group#sending-requests-in-parallel).
    
3. Study the responses and observe that multiple requests received a response indicating that the code was successfully applied. If not, remove the code from your cart and repeat the attack.
    
4. In the browser, refresh your cart and confirm that the 20% reduction has been applied more than once, resulting in a significantly cheaper order.
    

## Prove the concept

1. Remove the applied codes and the arbitrary item from your cart and add the leather jacket to your cart instead.
    
2. Resend the group of `POST /cart/coupon` requests in parallel.
    
3. Refresh the cart and check the order total:
    
    - If the order total is still higher than your remaining store credit, remove the discount codes and repeat the attack.
    - If the order total is less than your remaining store credit, purchase the jacket to solve the lab.

Instructor:

We load up the lab and we click "View Details", then we scroll down, we add an item to our cart by clicking "add to cart"

From here we go ahead and head to our cart

We see that we have a promo code that says "PROMO20"

We add our coupon, click apply and we see it was applied.

After that, we have had a lot of traffic in burp suite that we captured, we go check out our HTTP history,

We investigate the traffic and come across a POST request that says "/cart/coupon" from here we investigate the request and we see that it has a csrf and a coupon parameter

We send this to repeater

if we send the request in repeater to the web application we get a "coupon applied"

if we send it again we get a "coupon already applied"

We go back to our web app lab and we refresh, we remove the coupon, and go back to repeater

So the instructor says we will actually attempt to send the request multiple times (15 times)

so we will press ctrl+r for a total of 15 tabs in repeater, and from there we are going to click the + sign on repeater, create a tab group, name it race condition, and we will select all 15 tabs we created.

From here, we get a drop down menu in Send, and we will click the "Single Group in Parallel (single-packet attackl)"

we click that and we go back to our lab web app, and we refresh, and see that the coupon has been applied multiple times!

We click "place order" with our multiple coupons applied, and we get a completed lab!

There are two main points the instructor points that that are very important!

1) Find an endpoint where the limit is one, in this case it was the /cart/coupon always be on the look out for this
2) Sending requests in parallels, the  "Single Group in Parallel (single-packet attackl)" is very useful for this




