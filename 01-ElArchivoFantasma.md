# ElArchivoFantasma

**Event:** URJC CTF course — Clase 1  
**Category:** Forensics / Misc  
**Tools used:** `unzip`, `ls`, `ls -la`, `cat`

## Challenge

The challenge was provided as `ElArchivoFantasma.zip`. After extracting it, the README said:

> The system looks clean.  
> Too clean.  
> Find what someone tried to hide.

The objective was to inspect the recovered directory and locate the real flag.

## Recon / first look

I first extracted the archive and entered the challenge directory:

```bash
unzip ElArchivoFantasma.zip
cd ElArchivoFantasma
ls
```

The visible structure contained normal-looking directories such as `documentos`, `imagenes`, and `notas`, plus a `README.txt` file. Nothing immediately looked unusual.

Because the README specifically hinted that something had been hidden, I listed all files, including dotfiles and hidden directories:

```bash
ls -la
```

This revealed a directory that a normal `ls` did not show:

```text
.cosas
```

## What I tried that did not work

A very tempting file was:

```bash
cat documentos/flag.txt
```

However, its contents were only:

```text
No va a ser tan fácil
```

So `flag.txt` was just a decoy. The other visible text files also contained harmless messages and did not contain a flag.

## Solution

The key clue was the README's wording about something being hidden. On Linux, files and directories whose names begin with `.` are hidden from a normal `ls`, so I used:

```bash
ls -la
```

After spotting `.cosas`, I inspected it:

```bash
ls -la .cosas
cat .cosas/notas.txt
```

The file contained:

```text
URJC{hidden_in_plain_sight}
```

## Flag

```text
URJC{hidden_in_plain_sight}
```

## Takeaway

When a filesystem challenge hints that something is hidden, one of the first commands worth trying is `ls -la`. A filename that looks obvious, such as `flag.txt`, can also be a decoy, so the surrounding clues are more important than the filename itself.
