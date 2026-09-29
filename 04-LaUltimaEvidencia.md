# LaUltimaEvidencia

**Event:** URJC CTF course — Clase 1  
**Category:** Forensics / Misc  
**Tools used:** `unzip`, `ls -la`, `find`, `grep`, `less`, `echo`, `base64`

## Challenge

The challenge was provided as `LaUltimaEvidencia.zip`. After extraction, it contained hundreds of files and directories, most of them empty, together with several fake flags and a small number of meaningful text files.

The objective was to follow the forensic trail through the filesystem until reaching the final encoded evidence and decoding the real flag.

## Recon / first look

I extracted the archive and inspected the top-level structure:

```bash
unzip 'LaUltimaEvidencia.zip'
cd LaUltimaEvidencia
ls -la
```

There were many normal and hidden directories. Instead of opening hundreds of entries manually, I first looked for non-empty files:

```bash
find . -type f ! -empty
```

This reduced the noise considerably. One of the useful log files was:

```text
./sistema/logs/acceso.log
```

Reading it:

```bash
less sistema/logs/acceso.log
```

showed that an important piece of evidence had been modified and suggested looking for files connected to the beginning or origin of the activity.

## What I tried that did not work

There were several convincing decoys. For example:

```bash
less informes/flag.txt
```

returned:

```text
FLAG{final_flag}
```

but the nearby file `informes/honestly.txt` explicitly confirmed that this was not the real flag.

Another decoy was:

```bash
less evidencia/flag.txt
```

which simply said:

```text
No, no es la flag
```

The large number of empty files and directories was also intended to make manual inspection impractical.

## Solution

The log mentioned the beginning/origin of the activity, so I searched recursively for the word `actividad`:

```bash
grep -Rni "actividad" .
```

Besides the original log, this highlighted:

```text
./evidencia/registro.txt
```

I inspected it:

```bash
less evidencia/registro.txt
```

It said that the next clue was located in a file whose **name** contained `rastreo`.

I searched by filename:

```bash
find . -type f -iname '*rastreo*'
```

This returned:

```text
./sistema/backup/antiguo/rastreo_final.txt
```

Reading that file:

```bash
less sistema/backup/antiguo/rastreo_final.txt
```

revealed the next instruction: find a file whose **contents** contained `ULTIMA_PISTA`.

I searched recursively:

```bash
grep -Rnl "ULTIMA_PISTA" .
```

This identified:

```text
./evidencia/datos.txt
```

I opened it:

```bash
less evidencia/datos.txt
```

The message said that the definitive evidence was in a file whose **name** contained `terminal`.

I searched again by filename:

```bash
find . -type f -iname '*terminal*'
```

The result was:

```text
./documentos/notas/antiguas/terminal.txt
```

Its contents were:

```text
La flag es: VVJKQ3tFVklERU5DRV83SDNfTEE1VF9UUjRDM19XQTVfRjBVTkRfOVgyN0tRXzQ4MTZ9
```

The string had the characteristic Base64 alphabet and padding-free length, so I extracted the encoded field and decoded it:

```bash
echo "VVJKQ3tFVklERU5DRV83SDNfTEE1VF9UUjRDM19XQTVfRjBVTkRfOVgyN0tRXzQ4MTZ9" | base64 -d
```

This produced the real flag:

```text
URJC{EVIDENCE_7H3_LA5T_TR4C3_WA5_F0UND_9X27KQ_4816}
```

## Flag

```text
URJC{EVIDENCE_7H3_LA5T_TR4C3_WA5_F0UND_9X27KQ_4816}
```

## Takeaway

The main lesson was to reduce filesystem noise systematically instead of browsing manually. `find` is useful when the clue refers to a filename, while `grep -R` is useful when the clue refers to file contents. The challenge deliberately alternated between those two kinds of searches and finished with a Base64 decoding step.
