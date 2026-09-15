# SQL injection vulnerability allowing login bypass

**Source:** PortSwigger Web Security Academy
**Category:** SQL injection
**Date:** 2026-09

## The target

A shopping application with a standard login form asking for a username and a
password. The goal is to log in as the `administrator` user without knowing the
password, by exploiting a SQL injection flaw in the login logic.

## Reconnaissance

I logged in with obviously wrong credentials first, just to see the normal
behaviour. The application answered with "Invalid username or password." and
nothing else changed in the page or the response headers.

Then I looked at how the request was sent. The login is a `POST` to `/login`
with two parameters:

```http
POST /login HTTP/1.1
Host: LAB-ID.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

username=wiener&password=peter
```

Two user-controlled fields going straight into what is almost certainly a
database query. The username is the interesting one, because it is usually
placed in the query before the password is even checked.

## What I tried that did not work

My first instinct was to attack the password field with `' OR 1=1--`. It failed,
and the failure was informative: the app still said "Invalid username or
password." That told me the injection was probably not reaching a useful part of
the query through the password field, or the comment was landing in the wrong
place.

I also spent a few minutes assuming the response would tell me something if the
query broke — an SQL error, a 500, anything. It did not. The app swallowed
errors and always returned the same generic message. So I could not rely on
error messages to confirm the injection; I had to reason about the query shape
instead.

## The breakthrough

The realisation was that in a login query the username is used to *select the
row*, and the password is checked *against that row*. Something like:

```sql
SELECT * FROM users WHERE username = 'wiener' AND password = 'peter'
```

If I can inject into the username and comment out the rest of the line, the
password check disappears entirely. The row is selected purely on username, and
whatever password I send never gets evaluated.

## Exploitation

I set the username to `administrator'--` and left any value in the password:

```http
POST /login HTTP/1.1
Host: LAB-ID.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

username=administrator'--&password=x
```

The query the server builds becomes:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'x'
```

The `'` closes the username string, and `--` turns the rest of the line
(` AND password = 'x'`) into a comment, so the database ignores it. The query now
matches the administrator row on username alone, and I am logged in as
`administrator`.

## Root cause and fix

The application built its SQL query by concatenating user input directly into the
query string, so input could change the structure of the query instead of being
treated as data. The fix is parameterised queries (prepared statements), where
the username and password are bound as parameters and can never alter the query
logic, no matter what characters they contain.

## Takeaway

In any login form, the username field is the first place to test, because it is
usually consumed by the query before the password check. Closing the string and
commenting out the rest is the fastest thing to try.
