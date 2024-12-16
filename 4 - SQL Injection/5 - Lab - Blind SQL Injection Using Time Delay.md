# Blind SQL Injection Using Time Delays – Comprehensive Notes
[Link To the Lab](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)

**High-Level Summary:**  
This set of notes covers how to exploit **_blind SQL injection_** using **_time delays_** as an inference method. Unlike previous blind SQLi techniques that rely on differences in page content or presence/absence of messages, time delay attacks exploit synchronous query execution. By causing the database to pause (sleep) before responding, attackers can confirm that their injection succeeded. Once a time delay is confirmed, the same technique can be used to infer further information by making conditions dependent on data values.

---

**Definition Table:**

| Term/Concept          | Definition/Explanation                                                                                             |
|-----------------------|---------------------------------------------------------------------------------------------------------|
| **_Blind SQL Injection (Time-Based)_** | A type of SQL injection where the server does not return errors or visible indicators, but the attacker infers conditions by timing how long the server takes to respond. |
| **_Time Delay Functions_** | Database-specific functions that pause (sleep) the query for a specified duration (e.g., `pg_sleep(10)` for PostgreSQL, `SLEEP(10)` for MySQL). |
| **_pg_sleep(10)_**    | A PostgreSQL function that causes the database to pause execution for 10 seconds. Used to test if injection is successful by measuring response time. |
| **_SLEEP(10)_**       | A MySQL function that pauses execution for 10 seconds. Similar to `pg_sleep(10)`, but MySQL-specific. |
| **_WAITFOR DELAY_**   | A Microsoft SQL Server command that introduces a delay (e.g., `WAITFOR DELAY '0:0:10'` causes a 10-second pause). |
| **_dbms_pipe.receive_message_** | An Oracle-specific function that can introduce a delay, often used as `dbms_pipe.receive_message(('a'),10)` to delay for 10 seconds. |
| **_Synchronous Query Execution_** | The server waits for the database query to finish before returning a response, allowing attackers to notice delays in the response time. |

---

# PortSwigger Lab Description (Original Source)

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics and performs a SQL query containing the cookie’s value.  
>  
> Results of the SQL query are not returned, and no visible changes occur based on query outcome. However, because the query is executed synchronously, introducing a deliberate time delay (e.g., 10 seconds) can confirm that the injection worked.  
>  
> **Goal:** Cause a 10-second delay to solve the lab.

---

# PortSwigger Official Solution (Summary)

1. **Intercept the `TrackingId` cookie** in Burp Suite.
2. **Modify the cookie** to include a time delay function:
```

TrackingId=x'||pg_sleep(10)--

```
3. **Send the request** and observe a 10-second delay in the server’s response, confirming the vulnerability and completing the lab.

---

# Instructor Lab Notes (Original) – Fully Detailed and Reformatted

> **Note:** These are the instructor’s original notes, now explained and structured with added commentary (*in italics*) to clarify the reasoning and steps.

## Step 1: Identifying the Time-Delay Technique

- The lab title and description mention **time delays**, so we know to focus on payloads that cause the database to pause execution.
- Without this hint, we would have had to try numerous other injection methods first.
- **Key Insight:** Understanding the type of blind SQLi (time-based) narrows down the possible payloads drastically.

## Step 2: Consulting the SQL Injection Cheat Sheet

From the PortSwigger SQL injection cheat sheet, we find these time-delay functions:

| Database    | Delay Payload Example           |
|-------------|---------------------------------|
| Oracle      | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft   | `WAITFOR DELAY '0:0:10'`        |
| PostgreSQL  | `SELECT pg_sleep(10)`           |
| MySQL       | `SELECT SLEEP(10)`              |

*Note:* Each database engine has its own function for sleeping/delaying queries.

## Step 3: Choosing the Appropriate Payload

- Initially, we might guess the database type. Often, **PostgreSQL** or **MySQL** payloads are most commonly used in these labs.
- For MySQL, a typical payload:  
```

' AND SLEEP(10)--

```

or for PostgreSQL:  
```

'||pg_sleep(10)--

```

- Sometimes concatenation operators vary (`||` for PostgreSQL, `+` or `AND` for others). Experimentation is needed if we aren’t sure of the DB type.

## Step 4: Attempting the PostgreSQL Payload

- The instructor tries:
```

Cookie: TrackingId=c0Ki5i90865J7TM1 ' ||pg_sleep(10)-- -

```

- After sending the request, the response returns after approximately 20.1 seconds, not 10 seconds.

**Why 20.1 seconds?**  
- The doubled delay might indicate the query or function is executed twice or in multiple places in the application code.
- It’s also possible the application processes the cookie multiple times, resulting in the delay stacking.

**Important Consideration:**  
Time-based attacks must be carefully measured. If a 10-second delay appears as 20 seconds, it might mean the payload was triggered more than once. Always verify by resending the request to confirm consistent timing.

## Step 5: Verification and Adjustments

- Resending the request still results in ~20 seconds.
- This confirms that the injection worked but ran twice. While unexpected, it still proves the presence of a time-based SQLi.
- For the purpose of the lab, causing any controlled delay (10 seconds or more) confirms success. The lab is completed once we observe the intended delay.

**Key Takeaway:**  
Even if the delay is not exactly what we expected, a significant and consistent delay strongly indicates successful time-based SQL injection.

---

# Exam Readiness & Actionable Methodology

## Methodology Workflow

1. **Detecting Blindness:**  
 If no error messages or visible differences, consider time-based injection.
 
2. **Selecting Payloads:**  
 Check a known cheat sheet for database-specific time-delay functions.
 
3. **Testing Common Databases:**
 - PostgreSQL: `pg_sleep(10)`
 - MySQL: `SLEEP(10)`
 - Microsoft: `WAITFOR DELAY`
 - Oracle: `dbms_pipe.receive_message`
 
4. **Observe Response Times:**
 - If the server takes approximately 10 seconds longer to respond, the payload worked.
 - If unexpected delays (like 20 seconds), consider multiple executions or re-check the payload.
 
5. **Refine and Confirm:**
 - Resend requests for consistency.
 - Adjust payload syntax if initial tests fail.

## Troubleshooting Advice

- If `pg_sleep(10)` fails, try `SLEEP(10)` or other DB-specific payloads.
- Ensure the correct comment syntax for the database (`--`, `#`, `/* */`).
- Measure response time accurately. Use Burp’s Repeater or the browser’s network panel to confirm delays.
- If delays stack, it might mean multiple queries are running with your injected payload.

## Key Takeaways

- Time-based SQLi is about measuring delays rather than seeing visible changes.
- Using known functions from a cheat sheet saves time.
- Unexpected double delays may occur if the application processes the injected value multiple times.

---

# What to Do Next (Exam Simulation)

- Practice different time-based payloads on test environments to quickly identify the correct DB function.
- Familiarize yourself with Burp Repeater timing and response analysis tools.
- Keep a personal cheat sheet of time-delay functions for common databases.

---

**_Areas Needing Further Clarification_**:  
- Confirming the exact database type if time delays are ambiguous.
- Handling cases where multiple DB queries double the delay.

**_Further Research_**:  
- More PortSwigger labs focusing on time-based blind SQLi.
- Detailed DB-specific syntax references for time functions.
- Advanced binary search logic using time delays to extract data.

---

# Relevant Hashtags  
#BlindSQLInjection #TimeDelay #SQLInjection #WebHacking #PortSwigger #ExamPrep #BurpSuite 