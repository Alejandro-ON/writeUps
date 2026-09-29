# LaCajaNegra

**Event:** URJC CTF course — Clase 1  
**Category:** Misc  
**Tools used:** `unzip`, `ls -la`, `less`, `grep`, `sort`, `tail`, `python3`

## Challenge

The challenge was provided as `LaCajaNegra.zip`. The extracted directory contained a large amount of noise: around one hundred `clave_XXX.txt` files, several hidden directories, fake flags, and a large `caja_negra.txt` log.

The objective was to identify the correct key, follow it to the hidden message, decode that message, and recover the flag.

## Recon / first look

I extracted the archive and inspected the directory:

```bash
unzip LaCajaNegra.zip
cd LaCajaNegra
ls -la
```

Two files were immediately useful:

```text
pista.txt
caja_negra.txt
```

Reading the first hint:

```bash
less pista.txt
```

produced a clear instruction: `caja_negra.txt` contained many keys, they had to be ordered from smallest to largest, and the last one would lead to the message.

There was also a second hint:

```bash
less pista2.txt
```

which explained that the numbers to sort were in the third column.

## What I tried that did not work

The directory contained roughly one hundred files named `clave_XXX.txt`. Opening them one by one would be extremely inefficient, and many were deliberate distractions containing messages such as:

```text
Esta no es la clave correcta.
```

There were also fake flag files inside hidden directories, for example:

```text
No va a ser tan fácil
```

A direct recursive search for `URJC` did not reveal the final flag either, because the final message was encoded rather than stored in plaintext.

## Solution

The hint said that I needed to extract the `CLAVE` lines from `caja_negra.txt`, sort them numerically using the third field, and keep the greatest value.

The complete pipeline was:

```bash
grep "CLAVE" caja_negra.txt | sort -n -k3 | tail -1
```

This returned:

```text
CLAVE - 084
```

So the relevant key file was:

```bash
less clave_084.txt
```

Its contents pointed to a hidden file:

```text
.forms/mensaje_notes.txt
```

I opened it:

```bash
less .forms/mensaje_notes.txt
```

The file contained a long sequence of 8-bit binary values:

```text
01100010 01101100 00110100 01100011 01101011 ...
```

I decoded every 8-bit group as an ASCII character using Phyton: 

```text
python3 -c "print(''.join(chr(int(b,2)) for b in open('.forms/mensaje_notes.txt').read().split()))"
```

The decoded message was:

```text
bl4ck_b0x_84_f0und_7h3_m3ss4g3
```

Using the `URJC{...}` flag format used by the course, this gives the final flag below.

## Flag

```text
URJC{bl4ck_b0x_84_f0und_7h3_m3ss4g3}
```

## Takeaway

The important lesson was to process structured text instead of manually inspecting files. `grep`, `sort`, and `tail` can be chained with pipes to reduce a large dataset to one relevant value. The challenge also reinforced the need to inspect hidden directories and recognize binary-encoded ASCII when values are grouped into eight bits.
