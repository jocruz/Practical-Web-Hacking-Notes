
Slides:

Slide 1:
Concepts:
Application filtering / input validation
Obfuscation
Escaping
WAF and filter bypassing techniques

Slide 2:
Application filtering / input validation
Applications often filter input to weed out XSS and other payloads. However, we can use different techniques to bsfucate our payloads or find different payloads that cna pass undetected.
Filter Evasion Cheatsheet: [https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html)

Slide 3:
Escaping

Escaping is a technique used to secure ouput by ensuring that any special characters are treated as literal text, not as part of executable code snippet.

Payload:
```javascript
<script>prompt()</script>
```

Escaped:
&lt;script&gt;prompt()&lt/script&gt

Slide4:
WAF bypass techniques:

Web application Firewalls (WAFs) are security solutions designed to protect web applications by filtering and monitoring http traffic between a web application and the internet they operate according to a set of rules and policies that define benign and malicious web traffic

WAF bypasses are techniques that exploit the logic flaws or gaps in the WAF's detection mechanism. Since WAFs are often rule-based systems, they can be susceptible to evasion if the malicious payload does not match any of their known attack signatures or if its obfuscated in a way that waf fails to detect

Obfuscation: Making the attacks appear as normal traffic by using encoding, ccase changes, whitespace insertion, or other methods that do not match the WAF's signature patterns.

Evasion: Altering the syntax of the attack to evade detection without changing the semantics of the attack payload.

Blending: Mixing normal and attack traffic in a way that confuses the WAF and makes it harder to distinguish between the two.

Slide 5:
WAF baypass techniques - Parameter Pollution

Sometimes WAFS and applications handle http request differently, and we can use this to sneak payloads past a waf and into the application.

GET /?param=value&param=value
GET /?param=<script>prompt()</script>&param=1
GET /?param=1&param=<script>prompt()</script>

Slid 6:

WAF bypass techniques - Encoding

We can encode payloads to bypass WAFs that don't decode recursively or in proper order

Cleartext: prompt()
URL Encode: prompt%28%29
Unicode: \u0070\...
HTML Entities: &bsol;u0070&bsol;u0072&bsol;...

Slide 7:
With the same approach to filter evasion, we can use obfuscation to try payloads that might not be detected by a WAF.


Instructor notes:
Next, we have the tricky topic of bypasses and obfuscation, and I think a question that
I get more than anything else is, how do I bypass web application firewalls?
And we do have techniques for testing and bypassing WAFs, but there is no single technique
that will work every single time, and often you'll see random payloads being posted online
as WAF bypasses, however, they often only work on a single target or a single poly-configured
web application firewall.
I do recall seeing something on Twitter, somebody retweeting a quote-unquote WAF bypass saying
this is lit because it had some bytecode in it, I tested it against the mod security open
source firewall rules, and of course it was detected.
So this was probably for a CTF or a firewall that was very poorly configured.
But in this section, we're going to talk about filtering and input validation and obfuscation,
escaping, and bypass techniques.
So first up, applications obviously should take steps to ensure that the data we're
passing to them can't harm the application or its users in any way.
And there are many, many ways we can approach evading these types of filters, but I wanted
to share a resource with you that can help us achieve this, and that's the OWASP cheat
sheet series.
So let's take a quick look at that and walk through some of the different techniques.
So here we are at the XSS filter evasion cheat sheet, and there are a few of these that
I just want to highlight as good techniques to know, and then you can take a look at the
rest whenever you have time.
So of course we have the basic cross-site scripting testing without filter evasion,
so this is our initial payload that the article is using, and of course all it's doing is
opening up a script tag and then saying the source is here, so obviously we would have
to host this somewhere, and then closing this again, and we can use this to deliver payloads
quite easily, assuming that we can get this to execute.
Now the first one that I wanted to take a little bit of a closer look at is the from
car code.
So here if the system does not allow quotes of any kind, we can use things like eval and
from car code in JavaScript to create any payload that we need.
So we can see here that we have JavaScript, alert, and then we have a string from character
code 88, 83, 83.
So this is a payload that bypasses any filter that is filtering out single quotes.
And we can also do something similar with encoding.
So for example if we come down to here to the on error alert, we have a similar payload
where we're using the from character code, but we can also use encoding to obfuscate
our payload as well.
And this is a common technique and something that we should definitely try.
And also if we come down here and we see the embedded encoded tab, oops not this one, here
the embedded tab, we can see that we can use different approaches to break up our payload
as well.
And if this isn't processed properly, this can obfuscate our payload and bypass filters
and regex as well.
And hopefully you're now beginning to see how difficult it actually is to build filters
or write effective filters to protect against things like injection attacks.
And once again, using things like escaping and using things like parameterized queries
or prepared statements for SQL queries are generally a much more robust approach.
And once again we should of course do both, defence and depth is important, but nullifying
an attack entirely is generally better than trying to prevent it with blocklists and regex.
If you have time, then read through this article, try and understand some of the different payloads
and keep them as a sample, and then of course you can try out these techniques against targets.
When it comes to cross out scripting, escaping is critical, and it works by converting potentially
dangerous characters into a safe form that browsers do not interpret as executable code.
This prevents us creating a successful payload with special characters.
With escaping in place, even if we try to inject something like scripts, prompt scripts
into a web page, it would be rendered harmless by turning the less than greater than special
characters into something like ampersand, LT, semicolon, and ampersand, GT, semicolon,
as you can see on the slide.
And of course the browser displays this as text rather than executing it as JavaScript.
So let's talk about bypassing web application firewalls, but first, how do they actually
work?
Kind of from a high level.
Well, basically they intercept traffic and read the HTTP request and then decide if a
request is malicious or not.
And if it decides it is, based on a set of rules and rejacks, it will take some predefined
action.
Sometimes it will just send a response back, sometimes it will drop your quest entirely.
More advanced web application firewalls can do things like injecting headers on the fly.
They can also provide things like load balancing and rate limiting, and even block users that
it thinks are behaving in a weird way.
Wifi bypasses refer to ways in which we can trick the web application firewall into thinking
our traffic is legitimate.
For example, we just need to find a payload that matches what we need.
So in the case of cross-site scripting, we need a payload that will be executed in the
correct context and not match any of the patterns known by the web application firewall.
And we also need to not get blocked if we're fuzzing for this on the way as well.
It's worth noting that modern web application firewalls also take behavior into account
and repeated suspicious behavior usually leads to being blocked.
So in this case, going low and slow is often a good way to go.
Or if you can, identifying the web application firewall, and if it's an open source one,
for example, like mod security, you can do some local testing against that and then create
a more specific set of payloads before you go back to the application and attack it.
Here we have three high level methods of bypassing web application firewalls.
Now obviously obfuscation, evasion, and blending.
So obfuscation makes attacks appear normal or at least bypass the rejects that the web
application firewall is using.
And we can do things like, as you can see, changing case, adding white space, adding
different characters, et cetera, et cetera.
We can use evasion techniques by altering the syntax of the attack.
So for example, using things like prompt over alert, because alert is pretty much always
blocked, you might be able to find an alternative payload that achieves a very similar result.
And then blending, mixing normal traffic and malicious traffic in a way that confuses the
WAF and makes it harder to distinguish between the two.
Depending on the application as well, you might have the opportunity to break your payload
up into different requests.
Now a popular technique that has been used to bypass web application firewalls is something
called parameter pollution.
And this exploits the differences in how HTTP requests are interpreted by the web application
firewall versus the application.
And in this case, we might be able to break up payloads or sneak them in.
So as you can see here, in the example, we're using the same parameter name, but we're using
different values for those parameters.
And the bottom two get requests, for example, when we send param equals script prompt script,
and then param equals one, and then we try again sending param equals one, and then param
script prompt script, the web application firewall might look to grab the value of the
parameter.
And if it grabs the wrong one, for example, and processes it, it might think the request
is clean.
And then the application might use the other parameter, for example.
So parameter pollution is worth testing and can be used to bypass web application firewalls
sometimes.
Encoding and double encoding is a classic technique that doesn't often work these days,
but still worth testing as you will occasionally find some success.
And note that we're not just limited to double encoding or encoding twice.
We could encode three, four, five times, for example, before sending our payload.
Now of course, wefts should decode recursively and in the same way as the target application
does.
But again, similar to the parameter pollution, sometimes differences in how the payload is
processed, you might be able to make your payload appeal legitimate to the weft, but
then execute as code on the application.
In the example here, we're going from the clear text, and then we're URL encoding it,
and then that output is then turned into Unicode, and then that Unicode is turned into HTML
entities and so on and so on.
So in this case, we're basically layering our encoding and seeing whether there's any
difference between how the web application firewall and how the application is processing
this information.
And finally, of course, we have obfuscation.
So with the same approach to filter evasion, we can use obfuscation to try out payloads
that may not be detected by a web application firewall, and in a classic way, using capitals
and lowercase characters, for example, or using different payloads entirely.
That's it for now, and I'll see you in the next video.
