# Lab 01: SQL Injection in WHERE Clause Allowing Retrieval of Hidden Data

## Category
SQL Injection

## Difficulty
Apprentice

## Lab Objective
The goal of this lab was to exploit a SQL injection vulnerability in the product category filter and force the application to display unreleased products.

## Vulnerability Summary
This lab demonstrates a SQL injection vulnerability in the `category` parameter used by the application when filtering products.

The original query was structured as:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
User input was not safely handled, it was possible to modify the SQL query logic and bypass the condition that restricted results to released products only.

### How I Approached It

I used Burp Suite to intercept the request generated when selecting a product category.

From the lab description and query structure, I identified that the category parameter was being inserted directly into the SQL query. 
Since the value appeared to be placed inside single quotes, I tested whether I could break out of the intended query structure and alter the logic.


### Steps I Took
1. Opened the lab and selected a product category.

2. Intercepted the request in Browser URL.

3. Located the category parameter in the request.

4. Modified the parameter value to:

   '+OR+1=1--

5. Forwarded the modified request to the application.

6. Observed that the response now included unreleased products, which confirmed that the injection was successful.

### Payload Used

'+OR+1=1--



### Why the Payload Worked

The injected payload changed the query logic by appending an always-true condition:
 category = '' OR 1=1 --'

This caused the WHERE clause to evaluate as true for all rows. The comment sequence -- ignored the remaining part of the original query, including the AND released = 1 condition.

As a result, the application returned both released and unreleased products.

### Key Observation

The most important sign of successful exploitation was that the response displayed products that were supposed to remain hidden from normal users.

### Security Impact
In a real-world application, this kind of vulnerability could allow an attacker to:

- access hidden or restricted data

- bypass application logic

- enumerate sensitive records

- potentially extend the attack into more severe SQL injection scenarios depending on the backend database and query behavior

Even though this lab focused on hidden product data, the same weakness could be much more serious in production systems.

### Root Cause

The root cause was that user-controlled input was embedded directly into a SQL query without proper sanitization or parameterized handling.

The application trusted the category parameter and allowed it to modify the structure of the query.

### Remediation

To prevent this vulnerability:

- use parameterized queries or prepared statements

- avoid concatenating user input directly into SQL statements

- apply server-side input validation where appropriate

- use least-privilege database access to reduce impact if injection occurs


### What I Learned

This lab helped reinforce a core SQL injection concept: even a simple filter parameter can become dangerous when user input is inserted directly into a query.

It also showed how attackers can use a basic always-true condition such as OR 1=1 to manipulate application behavior and retrieve data that should not be visible.

Most importantly, it helped me understand how the structure of the original query guides the payload choice.

### Status

Solved

## Screenshot
![Lab solved evidence](../images/lab1-sqli.png)
