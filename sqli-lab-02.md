# Lab 02: SQL Injection Vulnerability Allowing Login Bypass

## Category
SQL Injection

## Difficulty
Apprentice

## Lab Objective
The goal of this lab was to exploit a SQL injection vulnerability in the login functionality and log in as the `administrator` user without knowing the actual password.

## Vulnerability Summary
This lab demonstrates a SQL injection vulnerability in the login form. Because user input was not handled safely before being included in a SQL query, it was possible to manipulate the query logic and bypass authentication.

Instead of validating the correct password for the `administrator` account, the application accepted a crafted input that altered the intended SQL statement.

## How I Approached It
For this lab, I did not use Burp Suite. I initially tested the login form directly through the application and tried different inputs to observe how the application behaved.

I also referred to external learning material to understand the pattern better, then used that understanding to continue testing until I reached the final working payload.

This helped me understand not just the solution, but why the payload worked.

## Steps I Took
1. Opened the login page.
2. Tested different inputs in the username and password fields.
3. Observed how special characters affected the login behavior.
4. Focused on modifying the `username` field to interfere with the SQL query.
5. Used a payload that targeted the `administrator` account and commented out the rest of the query.
6. Confirmed successful login as `administrator`.

## Payload Used

```text
administrator'--
```

## Why the Payload Worked

A login query often looks similar to this: 
SELECT * FROM users WHERE username = 'administrator' AND password = 'somepassword'

When the payload administrator'-- was entered into the username field, it changed the query structure.
The single quote ' closed the original string value early. After that, -- turned the rest of the SQL statement into a comment.

So the query effectively became:
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'somepassword'

Everything after -- was ignored by the database. That means the password check was no longer enforced, and the application logged in as the administrator user based only on the username match.

## Key Observation

The most important observation in this lab was that special characters such as ' changed how the backend interpreted the input. This showed that user input was likely being inserted directly into a SQL query without proper protection.

## Security Impact

In a real-world application, this type of vulnerability could allow an attacker to:

- bypass authentication

- gain unauthorized access to privileged accounts

- compromise sensitive user or administrative functions

- use the application as a foothold for deeper attacks

Authentication bypass through SQL injection is especially serious because it can directly expose administrative access.

## Root Cause

The root cause was unsafe construction of the SQL query using unsanitized user input.
Instead of treating the username as data only, the application allowed it to affect the structure of the query itself.

## Remediation

To prevent this vulnerability:

- use parameterized queries or prepared statements

- never concatenate user input directly into SQL queries

- validate and safely handle login input on the server side

- implement secure authentication logic and least-privilege database access

## What I Learned

This lab helped me understand how a single quote ' can be used to break out of the intended input value in a SQL query.

If the application builds a query like: username = 'user_input'
then adding ' can close that quoted string early. Once the string is closed, any additional characters may be interpreted as SQL syntax instead of normal input.

I also learned why -- is useful in SQL injection payloads. It marks the rest of the query as a comment, which allows an attacker to ignore remaining conditions such as password verification.
In this lab, that meant I could target the administrator username and bypass the password check entirely.
More importantly, this lab helped me connect the payload to the backend query logic instead of just memorizing a solution.

## Status

Solved
