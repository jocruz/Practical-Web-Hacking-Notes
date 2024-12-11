
Burp Suite solution:
1. Go to the lab and view `robots.txt` by appending `/robots.txt` to the lab URL. Notice that the `Disallow` line discloses the path to the admin panel.
2. In the URL bar, replace `/robots.txt` with `/administrator-panel` to load the admin panel.
3. Delete `carlos`.
   
Instructor notes:
When looking for IDOR we want to be thinking in ways of enumerating user abilities. So for example in the real world if we get access to Administrator account we want to see what we have access to with that user and its role and its functionality what can we do with that role, what can we access, what can we not do. We repeat this with other users on the web applications and their distinctive roles. The idea is we want to start laying out ideas of where to test IDOR. So that is the mentality we want to take on, and we should also take notes of such as we move through the web application

Okay so when we go into the lab, we take the url of the lab and we add in /robots.txt at the end of it

Web crawling is how bots like Googlebot browse the internet, following links to discover and index web pages for search engines. A **robots.txt** file helps control this by telling crawlers which parts of a site to avoid, such as `/admin/`. However, since `robots.txt` is publicly accessible, it can reveal sensitive endpoints, making it important to secure such areas with authentication rather than just hiding them from crawlers. Tools like **ffuf** can then be used by attackers to test and explore these hidden areas.

Okay so when we see what the /robot.txt adding it to the end of the URL reveals we see that it shows us

Disallow: /administrator-panel

So now we know that that is a hidden end point and we simply copy that and paste it at the end of the URL of the lab.

This brings us to the Unprotected Admin Functionality end point and we can delete users such as Wiener and Carlos. Deleting one of these completes the lab as this is an example of IDOR.

#rawnotes #lab #IDOR #accesscontrol 
