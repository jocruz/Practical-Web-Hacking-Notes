

Slide1:

What is Mass Assignment?

Mass Assignment occurs when applications (often frameworks) automatically bind parameters to objects.

This is a feature to make it a little easier to build objects based on incoming requests for developers.

Slide 2:
JSON from POST request

"user": {
"firstName": "jeremy",
"middleName": "jackson",
"lastName": "jones"
}
｝

Node js using spread operator:

const user = { ...req.body }

jeremy 
jackson
jones


Slide 3:
JSON from POST request

"user": {

"firstName" : "jessamy",

"middleName": "jameson",

"lastName": "johnson",

"admin": true,

}

Node js using spread operator

const user = {...req.body }

jessamy jameson johnson admin: true


How do we identify these parameters?

- ﻿﻿Fuzzing
- ﻿﻿Code review
- ﻿﻿JWT tokens
- ﻿﻿Leaky API endpoints
- ﻿﻿Front-end code
- ﻿﻿etc


Instructor notes:
Welcome to this section on mass assignment an important vulnerability to test for as many
frameworks automatically assign input parameters to model objects as a feature and of course this
is to help speed up and simplify development so let's take a look. So frameworks like Ruby on
Rails, Laravel and others offer features to simplify the process of taking a user input and
assigning it to objects properties which are then processed or saved in a database. I've also made
this mistake in the past when using the spread operator in my Node.js applications. Now if you're
not familiar with developing web applications this might sound a little bit confusing so let's walk
through an example. Here we have a post request coming into the registration endpoints of our
application. So what's happening here is we're defining the user with const user equals and the
spread operator with request dot body. So this takes all of the inputs like the first name, middle
name and last name that were supplied and creates the user objects. If we add more attributes then
they are also added to the user objects and if we miss out something like middle name they are not
added. So usually we'll have a model for the object and so sometimes if we miss an attribute
that's important it will still be added but the value might be null or false or some default value
that we've assigned. Sometimes we'll see attributes like user role and these might have a default
value of user but maybe in the future are able to hold the value admin for example so once the
administrator logs in they can then upgrade the user to a different level or in a lot of applications
they might have a different subscriber level roles or something like this. So here we create another
user but we're also passing in admin true and this is also added to the user object where it's
created and we can easily create admin users. Now of course this is a simplified example and we have
a lot of circumstances that we might have to contend with to actually find and exploit a
mass assignment vulnerability but it's a really interesting one as it requires us to understand
the application and the context in how objects are created and processed and stored. Now if you're
interested in looking a little bit deeper at some code on this it's covered in the practical API
hacking course where we step through the code of a lab so if you want to see it in action and
really read into it then definitely go ahead and check that out. Now if you're interested in looking
a little deeper at some code on this vulnerability it is covered in the practical API hacking course
where we step through the code of a lab so definitely check that out if you want to see
an example in the style of code review. So finally you might be thinking how do we know
what the parameter name that the application uses since it's probably mostly handled in the back end
it might not ever be revealed to us. Well there are a few ways to try and find out. Here are some
different ways we can discover these attributes so that we can craft custom objects to do our bidding.
Of course we can try and fuzz the application creating objects or modifying objects with
custom values. We can also use code review if the application is open source or if we're able to get
the source code. We sometimes can see things in JWT tokens so if we decode them and we see a claim
like a user role claim or a user level claim or something like this. Sometimes leaky API endpoints
will return a lot of data more than they should do so for example if you go to your account and it
dumps everything to do with your account you might see some values in there that we can use to target
with mass assignments and then of course sometimes in the front end code we can find
things as well. So let's walk through a lab to see how we can find and exploit this vulnerability.

