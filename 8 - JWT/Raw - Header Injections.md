
Slide1:
Header Injection
With Header Injection we can instruct the server which key to use when verifying the signature

Headers that assist in this attack are:

JWK (JSON Web Key)
JKU (JSON Web Key Set URL)
KID (Key ID)

Slide2:

JWK (JSON Web Key) Header Injection

We can use the JWK format to add a public key within the token

The Server may then use this public key to verify the forged token rather than the legitimate key




Instructor notes about Header Injection:

When we look at header injection, we're essentially trying to trick the server into using a key
that we provide to verify the token, instead of the legitimate key that it's supposed
to use. So even though things might get a little bit complicated in terms of names and
types of headers, try to keep in mind what our goal is, and this will help you stay on
the right track.
But in a slightly different way, we're exploiting the fact that the server isn't properly
validating one or a few of the following headers, which are the JWK, JKU and KID fields.
So what do these actually do, though?
So the JWK JSON WebKey header, this is a JSON object representing a key. So for example,
we can use this to pass a key with our token. The JKU header, JSON WebKeySetURL, is a URL
that can be used to fetch a key or a set of keys. And the KID or key ID is an ID that
servers can use to identify which key to use if there are multiple keys to choose from.
So these are all important headers that refer to keys when validating a token. And of course,
if we control the key that's used to validate a token, we can easily forge our own tokens.
Now the injection technique we're going to look at comes from the ability to specify
a JSON WebKey or JWK as a header. A JWK is a JSON object that represents our key. The
JWK format can be used directly. And we can use this format to directly include a public
key in the header. In the standard JWT process, the server would use a known public key to
verify the signature of the token, ensuring its authenticity and integrity. In a standard
JWT process, the server would use a known public key to verify the signature of the
token, ensuring its authenticity and integrity. And of course, it's signed by the private
key and then uses the public key to verify this. However, with JWK header injection,
we can modify the header to include a JWK with a public key that we control. If the
server isn't properly validating the JWK against a known list or a trusted list, it
might use this injected key to verify the signature of our token. This allows us to,
of course, forge tokens that the server will accept as legitimate. The steps for header
injection might be a little bit tricky to visualize. So let's walk through a lab together to see
how we can pull off this attack. All right, let's go.
