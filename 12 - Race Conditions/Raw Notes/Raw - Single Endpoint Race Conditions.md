![[Pasted image 20250113153648.png]]

Instructor transcript:

When we first look at single endpoint race conditions, we might think to ourselves, ah, looks a little bit like limit overrun.
But actually we need to think about collisions.
So in this case, potentially what steps are happening behind the scenes and how sending multiple requests might impact that instead of
sending as many requests as possible within the race window.
So here we have an interesting example from Port Swigga that we can walk through.
Two requests are coming in, but they both have a number of actions that are going to be carried out at different times during the race window.
So the password reset mechanism, in this case, stores the user ID and the reset token in the user's session.
When we send requests in parallel to the password reset endpoint from the same session,
but with two different usernames, we can potentially cause a collision.
The first request comes in and our race window opens up and the user is set to hacker.
Shortly after, another request comes in and the user is then set to victim.
This request continues to be processed, so we have a reset token and that's then sent to the victim.
And finally, the reset token is set to 1234 and sent to the user hacker.
So the session now contains the victim user ID, but the reset token is sent to the attacker.
And you might be thinking, this is an unreliable situation and you're right,
we will need to give this quite a few goes before we can get this collision to work.
However, emails in particular often give us a decent race window to work with.
And with modern tools, sending hundreds or thousands of attempts is fairly trivial.
Let's walk through another lab together to see this in action before we move on.
And of course, if you're feeling confident, then by all means,
have a go at the lab yourself before coming back for the walkthrough.
All right, so let's take a look at this guided lab.


