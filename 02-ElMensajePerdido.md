# ElMensajePerdido

**Event:** URJC CTF course — Clase 1  
**Category:** Forensics / Misc  
**Tools used:** `unzip`, `ls -la`, `cat`, `grep`

## Challenge

The challenge was provided as `ElMensajePerdido.zip`. Its README explained that the files came from a compromised computer and that, among all the recovered documents, there was an unusual file named only `-`.

The objective was to find that file, read its contents, and use the information inside it to locate the flag.

## Recon / first look

I extracted the archive and listed the directory:

```bash
unzip ElMensajePerdido.zip
cd ElMensajePerdido
ls -la
```

There were several ordinary-looking folders (`documentos`, `logs`, and `notas`) containing many text files. The unusual entry was:

```text
-
```

That matched the hint in the README.

## What I tried that did not work

Reading random notes, logs, and documents one by one only produced normal system messages. There were enough files that manually opening every file would be inefficient.

The filename `-` is also slightly awkward on Unix-like systems because many commands interpret a single dash as standard input rather than as a normal filename. Therefore, using the path explicitly is safer than treating it as an ordinary argument.

## Solution

I read the strange file by prefixing its name with `./`:

```bash
cat ./-
```

It contained:

```text
El mensaje que buscas contiene la palabra:

URJC
```

Instead of checking every file manually, I searched recursively for that string:

```bash
grep -R "URJC" .
```

The relevant result was:

```text
./logs/log4.txt:URJC{Grep_7h3_5ecr3t_M3ss4g3_X9q!2Lm}
```

I then confirmed it directly:

```bash
cat logs/log4.txt
```

## Flag

```text
URJC{Grep_7h3_5ecr3t_M3ss4g3_X9q!2Lm}
```

## Takeaway

This challenge introduced two useful ideas. First, filenames can be intentionally awkward, and prefixing them with `./` can prevent special interpretation. Second, `grep -R` is much faster than manually inspecting many text files when a known word or pattern is available.
