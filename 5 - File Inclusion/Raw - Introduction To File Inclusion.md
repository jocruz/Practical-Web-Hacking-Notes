
what is File Inclusion?

Modular Design:
Modern applications are designed in a modular fashion for maintainability, scalability and efficiency.

Instead of a single, monolithic script, applications are broken down into multiple components or modules.

This modular design involves separating the code into different files base don functionality.

File Inclusion:
To use these separate modules, a main application file includes or imports them as needed. In web applications, this is often done using directives like include, require in PHP, import in python or require in Node.js

This inclusion mechanism allows for reusing code, such as libraries, configurations, templates, or other functional modules.

Many applications dynamically include files based on user input or other variables.

This is done to load resources specific to a user's request, like different language files, user specific configurations or templates.

What is File Inclusion

Improper Validation of User Input:
When the path of the included file is constructed using user input without proper validation or sanitization, an attacker can manipulate this path.

This manipulation can lead to including files that were not intended to be accessible.

Lack of Proper Access Controls:

Applications that do not implement robust access controls on file directories and resources are more susceptible to these vulnerabilities. 

This lack of control can allow unauthorized access to sensitive files.

Local File Inclusion (LFI):

In LFI, attackers exploit this vulnerability to include files that are already present on the server, such as configuration files, logs or even executable code.

This can lead to information disclosure, server-side code execution or privilege escalation.

Remote File Inclusion (RFI):
RFI occurs when an application allows the inclusion of remote files through URLs.

If not properly secured, an attacker can include a remote file (like a malicious script hosted on their server) into the application.

Path Traversal:
Path traversal is a vulnerability that occurs when web applications allow input to influence the path used to access resources. 

By manipulating variables that reference files (like ../ in Unix-based systems or ..\ in Windows), we can move up directories and access files or directories that should not be accessible through the web application.

Absolute Path:
/path/to/file.txt

Relative Path:
/var/www/html/../../../etc/passwd