
Lab: [https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)


Burp Suite Solution:

1. Select the **Live chat** tab.
2. Send a message and then select **View transcript**.
3. Review the URL and observe that the transcripts are text files assigned a filename containing an incrementing number.
4. Change the filename to `1.txt` and review the text. Notice a password within the chat transcript.
5. Return to the main lab page and log in using the stolen credentials.


Instructor Solution:

We head over to the lab and we investigate the web application. From there we see we are given the option to head to the live chat. We head over to the live chat.

We see we are able to interact with the Live Chat, and the instructor makes not that this is an indication that the web application is using a web socket. From there we are able to submit a message and we do. We say "Hello there" to see what we get from the interaction.

From there we click "Send" and then if we refresh the page we see that the Live Chat is actually saved. We click the next button "View Transcript" and we get a download called "2.txt"

From my own intuition I can already tell that the number 2 is a dead give away to what we might be able to exploit.

We head over to Burp Suite and we see what type of traffic we can investigate.

Investigate the Method and URL in Burp Suite. 

We see that we find some GET request with the URL /chat and the status code is 101. The Instructor says that the header in the response is HTTP/1.1 101 Switching protocol and the Upgrade: websocket proves that we are using websockets.

We can investigate this further if we head over to the WebSockets history on Burpsuite.


If we keep investigating the traffic we come across a POST request with the /download-transcript/2.txt and a GET request with /download-transcript/2.txt. Since we downloaded we will want to investigate the GET request. 

We send this over to the Repeater (ctrl+r)

After we send it to repeater we see the header has 
GET /download-transcript/2.txt HTTP/2

We put a positional marker on the number 2 in "2.txt"

We head over to payloads tab in repeater and we change the payload type to Numbers.

We set the From to 0 to 50.

We start the Attack.

From here we can investigate the results and we can see that there is a lot of status codes for 400 (investigating this we see that the response contains "No Transcript") so if we reorder this then we can see that there are some 200 status codes. From there we can see that we get a status code of 200 and we investigate the responses of these 200 status codes and we do come across a transcript text.

There we see that the Live chat transcript contains a password!


Screenshot of the payload and transcript containing password:
![[Pasted image 20241211090702.png]]

The victims name is carlos as provided by the lab and now that we have a password that isn't ours we can return back to the lab web application and we head over to My account and we now use the credentials to log in

Username: Carlos
Password: **_Password we found in the transcript**

And we complete the Lab
