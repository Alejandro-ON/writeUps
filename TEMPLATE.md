<!--
  HOW TO USE THIS TEMPLATE
  ------------------------
  1. Copy this file into the right folder under writeups/ and rename it.
     Use lowercase and dashes: writeups/portswigger/blind-sql-injection.md
  2. Fill in the sections below and delete the comments (the <!-- ... --> blocks).
  3. Add a row linking to it in the table in README.md.

  Target length: 600-900 words. If a section has nothing interesting in it,
  delete the whole section instead of padding it.
-->

# <!-- Lab / machine / challenge name -->

**Source:** <!-- PortSwigger Web Security Academy / Hack The Box (retired) / CTF name -->
**Category:** <!-- SQL injection, access control, XSS, privilege escalation... -->
**Date:** <!-- YYYY-MM -->

## The target

<!--
  Two or three sentences. What is the application, and what was the goal?
  Write it so someone who has never seen this lab understands the objective.
-->

## Reconnaissance

<!--
  What did you actually look at before touching anything?
  Pages visited, parameters in the URL, request headers, error messages,
  anything in the HTML source. Concrete observations, not a tool dump.
-->

## What I tried that did not work

<!--
  THE MOST IMPORTANT SECTION. Do not skip it.

  For each dead end: what you thought was happening, what you tried,
  and what the response told you that ruled it out.

  This is what separates your writeup from the fifty identical ones that
  only show the clean solution. It shows how you narrow things down.
-->

## The breakthrough

<!--
  The single observation that changed everything.
  What did you notice, and why did it point at this vulnerability class?
-->

## Exploitation

<!--
  The working steps. Use code blocks for requests, payloads and commands:

  ```http
  POST /login HTTP/1.1
  Host: example.web-security-academy.net

  username=administrator'--&password=x
  ```

  Explain what each step does. A wall of commands with no explanation
  is exactly what makes a writeup worthless.
-->

## Root cause and fix

<!--
  Two or three sentences. What was the underlying coding mistake,
  and how should it have been written? Name the correct defence
  (parameterised queries, server-side authorisation check, output encoding...).
-->

## Takeaway

<!--
  One or two sentences. What will you look for faster next time?
-->
