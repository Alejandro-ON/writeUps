# SQL injection vulnerability allowing login bypass
 
**Source:** PortSwigger Web Security Academy
**Category:** SQL injection
**Date:** 2026-09
 
## The target
 
In front of us there is a shopping application which contains a SQL injection
vulnerability. My job is to find a way to enter the website as an administrator
by performing a SQL injection attack.
 
## Reconnaissance
 
The first thing I did was to go to the login page, since the lab is about login
bypass. It had fields to type a username and a password, so I entered some
example credentials. After that attempt I was greeted with an "Invalid username
or password" message, and nothing else happened.
 
For this exercise I used the Burp Suite Community tool, which lets me use a proxy
to intercept the request and inspect its body. In Burp I intercepted this request:
 
```
POST /login HTTP/2
Host: 0aa5000003adb383825206700027007b.web-security-academy.net
Cookie: session=ID55zz2KICHT3m8G3UZrmHEEQC9TiT7C
Content-Length: 77
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="151", "Not=A?Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: es-ES,es;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/151.0.0.0 Safari/537.36
Origin: https://0aa5000003adb383825206700027007b.web-security-academy.net
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aa5000003adb383825206700027007b.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
 
csrf=mZINrYEpE2Y7SoB7pYK44TDNnzwtq5LA&username=administrator&password=example
```
 
Here I could see the part I would be working with:
`username=administrator&password=example`.
 
## What I tried that did not work
 
The first thing I did was to attack the request with `' OR 1=1--` in the password
field. I did it because I had seen it somewhere a while ago and I used it without
thinking. It did not work, probably because I was attacking the wrong part of the
query.
 
The other thing I tried was typing random text looking for a different kind of
error, but the response was always the same "Invalid username or password"
message, which did not help.
 
## The breakthrough
 
After thinking for a while, I reminded myself what I was actually doing: a SQL
injection attack. That means I am trying to interfere with a database query,
probably one that reads from a `users` table with at least two fields, the
username and the password.
 
At first I thought the `&` characters I saw in the intercepted body might be the
SQL `AND`, but that is wrong: those `&` are just the separators between the form
fields (`csrf`, `username` and `password`) in the request body, and they have
nothing to do with SQL. The `AND` is not visible anywhere in the request; I
deduced it. A login has to check that the username exists AND that the password
matches, so those two conditions are almost certainly joined with `AND`. That led
me to guess the query looked something like:
 
```sql
SELECT * FROM users WHERE username = 'smth' AND password = 'smth'
```
 
## Exploitation
 
Back in Burp, I intercepted the login request again and changed the `username`
field in the body to `administrator'--`, leaving the password as any value. The
modified body looked like this:
 
```
csrf=mZINrYEpE2Y7SoB7pYK44TDNnzwtq5LA&username=administrator'--&password=example
```
 
Then I forwarded the request. With this input, the query the server builds
becomes:
 
```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'example'
```
 
The `'` closes the username string, and `--` turns the rest of the line
(` AND password = 'example'`) into a comment, so the database ignores it. The
query now matches the administrator row on the username alone, and I was logged
in as `administrator`.
 
## Root cause and fix
 
The vulnerability exist because the website introduces the text that you write inside of the query so an imput like this can easily change the structure of it. A way of fixing it is to use parameterised queries
(prepared statements), where the username and password are sent to the database
as separate parameters and can never alter the query logic, no matter what
characters they contain.
 
## Takeaway
 
A SQL injection attack takes advantage of a vulnerability by tricking the way the application talks to its SQL database. In this case we removed the password check by treating it as a comment, using administrator'--: the ' closes the username string and the -- comments out the rest of the query.
