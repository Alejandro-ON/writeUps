# SQL injection UNION attack, determining the number of columns returned by the query

**Source:** PortSwigger Web Security Academy
**Category:** SQL injection
**Date:** 2026-09

## The target

In this lab we were at a website that shows a lot of products where you could
filter them by category. The objective here was to find out how many columns the
query returns.

## Reconnaissance

On this website we found the following URL:
https://0a2e00bf04ffc3368082081800d400d7.web-security-academy.net/

So the first thing I had to try was to test whether the URL was vulnerable to SQL
injection, by testing some queries like:
https://0a2e00bf04ffc3368082081800d400d7.web-security-academy.net/filter?category=Pets'+OR+category='Gifts'--

and indeed it gave us back a list of products from the 'Pets' or 'Gifts'
categories, proving that this URL is vulnerable.

## What I tried that did not work

Here I made a couple of mistakes:

1. First, I struggled to understand how the URL is translated into a SQL query,
often leaving the fields without the mandatory `'`, like:
filter?category=Pets OR Gifts'--
filter?category=Pets OR category=Gifts'--
instead of the correct one, which is
filter?category=Pets'+OR+category='Gifts'--

2. Secondly, I was stuck on how to discover how many columns the query returned.
I was trying blindly to make a UNION with different tables, hoping to find one
that gave me back a 200 OK message, but I was going completely blind because I
didn't know the name of any other table. On top of that, a UNION also requires
the two queries to return the same number of columns and with compatible data
types (int, varchar, etc.).

## The method

After a lot of research I found a wildcard: NULL. NULL is an empty value that can
be inserted into any type of column; it works for int as well as varchar, bool,
etc. Thanks to this, I just had to find the right number of NULLs needed.

## Exploitation

The solution was to build a UNION with as many NULLs as I needed. To do this I
used the Burp Repeater tool, which let me quickly try different queries and get
back either an HTTP/2 500 Internal Server Error or a 200 OK.

This was the sequence I received:
  - UNION SELECT NULL           -> 500
  - UNION SELECT NULL,NULL      -> 500
  - UNION SELECT NULL,NULL,NULL -> 200
Which means the query returns 3 columns, and the lab was successfully passed.

## Root cause / Takeaway

In this lab I learned how to properly navigate URL queries at a low level and how
the UNION operator works. I also learned about the NULL wildcard and how it helps
to find how many columns a query returns, bringing me closer to other techniques
like stealing data with a UNION attack, which I hope to learn soon.
