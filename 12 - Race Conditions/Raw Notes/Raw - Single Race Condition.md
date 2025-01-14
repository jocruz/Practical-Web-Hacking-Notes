Slide 1:

What is a Race condition?

- Race conditions occur when request are processed concurrently.
- Can lead to multiple threads interacting with the same data at the same time, causing a collision.
- As attackers, we can use carefully timed request to cause collisions and exploit the resulting behavior


![[race condition.png]]


Welcome to this section on race conditions, an exciting vulnerability that is much easier
to test thanks to the updates in VerbSuite.
So we'll be exploring a few different scenarios, and remember that when we're on the lookout
for these kinds of vulnerabilities, we should be considering the application logic and
behavior.
Race conditions are closely related to business logic issues, as we're essentially tricking
the application into processing our requests in an unintended order and see the results.
So what is a race condition exactly?
Well when we send requests to an application, generally the expectation is that they are
processed in the order that they are received, and that's an action or a block of logic
is executed entirely before moving on.
However, we can induce race conditions so that something else happens before the application
has finished processing the original request.
Often this means we can interact with data multiple times before it's updated, or do
things like insert extra items into our basket before a payment process is complete.
Timing of course is always key.
So speaking of timing, here we have a race window, and in the diagram you can see a
classic time of check, time of use issue.
The first three requests come in, and check to see if this one-time coupon is valid.
If it is, it continues to be processed, and then at the point where the coupon is set
to be invalid by the first request, no further requests can use it.
So in this case, we've overrun the limit of one-time use by getting a few requests in
before the logic of the app is setting that coupon to used.
So this is known as limit overrun.
The race window might be a little bit more complex with other situations.
For example, if we want to add more items to a basket while the payment is processing,
the race window will be between the initial basket check and the payment validation.
During this time, we can potentially add more items, but more on that later on.
For a limit overrun, we want to target things like one-time actions or actions that have
a limited number of uses.
And to exploit this, we need to find the endpoints and then send multiple requests in parallel
to see if we can get these processed before the race window closes.
Let's take a look at a lab together so that we can see the methodology for testing and
exploiting this, and also get used to the tools that we can use within bub suite to
find and exploit race conditions.
