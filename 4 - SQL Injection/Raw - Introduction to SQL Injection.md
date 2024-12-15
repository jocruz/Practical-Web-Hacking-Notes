What is SQL Injection?
SQL injection allows us to manipulate queries that are made to a database and typically leads to:
- Exposure of sensitive data
- Data manipulation
- Denial of Service


There are three main techniques that we can use:

Manual Payloads:
- ' and 1=1-- - 
- ' and 1=2-- - 

Code Review:
- Search for Dynamic SQL statements

Fuzzing
- Send all the payloads

Unless we directly have access to the source code we will be doing Fuzzing or Code review.

Our methodology needs to be efficient because the web application might be large and there might be a lot of testing that needs to be done.

So we will need to start Fuzzing End Points and Parameters to spot any unusual behavior

Second, we might use a tool like SQLmap to verify our findings or extract some data and exploit it. Sometimes SQLmap might not work because there are some special characters that will not work or there is a filter in place that will prevent us from using the tool

We can move onto manual testing if nothing else works.

Our priority might change depending on what role we are in, such as Application Security, Web App Pen Testing, Bug Bounty etc.

Lets take a look at Dynamic Queries vs Parameterized Queries vs Stored Procedures

Dynamic Queries:

```sql
$query = "SELECT id, name, price FROM products";
$result = mysqli_query($connection, $query);

String query = "SELECT id, name, price FROM products";
Statement Assads = connection.createStatement();
ResultSet Assads = statement.executeQuery(query);

const query = "SELECT id, name, price FROM products";
connection.query(query, (error,results, fields)=> {
	if (error) throw error;
	//process results
});
```

So here we have the user input, which is the category variable, is passed into the SQL statement and then executed. This breaks our secure design principle of not mixing data and code. And of course, if we pass in something that changes the statement, we have SQL injection. Now, the top example is PHP, the second one is Java, and the third one is Javascript. You don't need to memorize or even recognize these.

Now, you might be thinking, okay we can sanitize the input before it's passed to the query, and that's okay, but if there is an issue with our sanitization or some kind of mistake, then once again, we might be vulnerable to SQL injection. 

So it's actually better practice to use prepared statements or parameterized queries and that is what we have here.

So, first up, the difference between prepared statements and parameterized queries, as these are often used interchangeably. 

So, a prepared statement refers to a feature where a statement is compiled once and then can be executed multiple times with different parameters. 

The statement preparation and execution are distinct steps, and the prepared statement is stored in a compiled form by the database system.

So, once the statement is prepared, it can be executed repeatedly by binding new values to the placeholders or parameters without recompiling the SQL statement, which can improve performance, although in reality, from tests on large databases that I've seen with this, the increase in performance is almost negligible. 

Now, parameterized query generally refers to the inclusion of placeholders in a SQL query that are replaced with actual values at runtime. It might not always involve a distinct preparation and execution step and parameterized queries are used to ensure that user input is treated as data and not as part of the SQL, and of course, this prevents SQL injection. So, parameterized queries are more of a general concept than a specific implementation covering any SQL that accepts parameters. 

So, despite these differences, the primary goal of both is the same, to separate the SQL code from the data, which helps prevent SQL injection and can also improve performance.

In many Programming languages and database systems, the terms are used as synonyms because they both achieve this goal through similar mechanisms. If you do get an interview question about how to prevent SQL injection, your primary answer should be prepared statements or parameterized queries, not input validation. Though, of course, we should be doing both, we should be doing input validation, but this is more to achieve defense in depth and neither should be left out.

ORM (Object Relational Mapping):

Now, ORMs are a bridge between the object oriented programming languages we use to build our applications and the relational databases that store our applications data. Rather than writing complex SQL statements and converting database types to application data structures, ORMS allow us to interact with the database using the programming languages own constructs.

This abstraction simplifies data manipulation and allows us to focus on building robust web application logic and with an ORM, we can easily perform CRUD operations, so create, read, update and delete, without writing SQL. 

SQLi and ORM:
### SQLi and ORM

**Injection Flaws:**  
ORM can both introduce and mitigate injection vulnerabilities. While ORMs generally reduce the risk of SQL injection by using parameterized queries, misconfigurations or improper use of ORM features can still lead to injection vulnerabilities.

**Misconfiguration:**  
Incorrect configurations of ORM tools can expose sensitive data, lead to data leakage, or open avenues for exploitation.

**Performance Issues:**  
ORMs can generate inefficient queries that can be exploited to cause Denial of Service (DoS) attacks through resource exhaustion.

**Logic Flaws:**  
ORMs abstract a lot of the database handling logic, which might mask potential business logic vulnerabilities if the pentester is not aware of how the ORM works.

---

So, while ORMs simplify database interactions, they are not without their challenges. First, there is still a risk of injection flaws, although often this is quite minimal. ORMs typically use parameterized queries to prevent SQL injection, but if they are imporoperly used, then they can sometimes still be vulnerable. Then, there is also the potential for misconfiguration, if an ORM is not configured correctly, it could inadvertently expose sensitive data or open up avenues for attacks. ORMs also create less efficient queries, leading to performance bottlenecks, that can lead to denial of service and finally, by abstracting the database logic, ORMs can mask the underlying business logic vulnerabilities, that might not be immediately apparent to developers or testers. 

So, using an ORM can provide some protection against SQL injection, but it is not entirely foolproof and by adding another layer of abstraction to our application, we are creating more complexity, which of course means, that it can be more difficult to build securely. 

ORM Example:
### ORM Example

#### Sqlite3 (traditional SQL)

```python
# create new user
c.execute("INSERT INTO users (name, age) VALUES (?, ?)", ('Alice', 24))

# fetch a user by name
c.execute("SELECT * FROM users WHERE name=?", ('Alice',))
user = c.fetchone()
```

#### Sqlite3 (with an ORM)

```python
# create a new user
new_user = User(name='Alice', age=24)
session.add(new_user)
session.commit()

# fetch a user by name
user = session.query(User).filter_by(name='Alice').first()
print(user)
```

---

In this example we compare some Traditional SQL operations with our ORM counterparts using SQLite 3. In the traditional SQL approach, we use SQL to interact with the database, we insert a new user by explicitly preparing a SQL statement with placeholders for values, to prevent injection and then we execute it with those parameters. Similarly, we fetch the data with a specific SQL query, but noticed how we are deeply involved with writing and defining the SQL syntax. Switching to the ORM approach, we create a user Object, just like any other object in our programming language and add it to the database session. 

The ORM takes care of generating the corresponding SQL commands, fetching a user is as simple as calling a method on our session and filtering by the user's name. 

Here, the ORM translates our object-oriented commands into SQL queries behind the scenes and the abstraction makes it feel like we are working with plain objects rather than a database, which is great from a development perspective. Now, just to clarify, using an ORM isn't necessarily a bad thing from a security perspective, it's just that the limitations need to be understood and it needs to be implemented and used as intended. ORMs are not a security tool and whilst they do have some security benefits, once again, they can be misconfigured or misused as well. 
