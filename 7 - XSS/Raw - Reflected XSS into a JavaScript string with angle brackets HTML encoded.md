Description:

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the `alert` function.

Portswigger solution:
1. Submit a random alphanumeric string in the search box, then use Burp Suite to intercept the search request and send it to Burp Repeater.
2. Observe that the random string has been reflected inside a JavaScript string.
3. Replace your input with the following payload to break out of the JavaScript string and inject an alert:
    
    `'-alert(1)-'`
4. Verify the technique worked by right clicking, selecting "Copy URL", and pasting the URL in the browser. When you load the page it should trigger an alert.


Instructor Solution:

Once again similar to the other lab we type in qwerty in the search box, we hit submit, and this causes traffic in burp suite, after that, we are going to go ahead, and search for the POST request that shows where we typed in qwerty, we find it,

and we investigate the response, and we search for our value qwerty and see where it appears.

We see that it appears in two places, in h1 tags, and in a javascript section and gets stored in a variable called searchterms

var searchTerms = 'qwerty';

so we can try to escape this by using the Instructor payload: qwerty';prompt()//';

we comment out the end of the code to make sure it doesnt break in this case its the semicolon ending the line of code.

Once we go back to the web application and we submit qwerty';prompt()//'; we get a prompt on the screen and the lab is then completed



