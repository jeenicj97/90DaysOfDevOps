# Linux File Handling — Day 06

A simple and repeatable practice flow for Linux file handling: reading, writing, and appending text files.

---

## Step 1 — Create the File

```bash
touch notes.txt
```

> Creates an empty file named `notes.txt`.

---

## Step 2 — Write Lines Using `>` and `>>`

```bash
echo "Linux file practice Day 06" > notes.txt
echo "Learning file read and write commands" >> notes.txt
echo "Redirection operators are important" >> notes.txt
echo "Logs are stored as text files" >> notes.txt
echo "Configs are usually text based" >> notes.txt
```

| Operator | Behaviour |
|----------|-----------|
| `>`      | Creates or **overwrites** the file |
| `>>`     | **Appends** new lines to the file |

---

## Step 3 — Use `tee`

```bash
echo "tee writes and displays output together" | tee -a notes.txt
```

- `tee` displays output on screen **and** writes to the file simultaneously
- `-a` flag means **append** (does not overwrite)

---

## Step 4 — Add a Few More Lines

```bash
echo "cat reads complete files" >> notes.txt
echo "head reads starting lines" >> notes.txt
echo "tail reads ending lines" >> notes.txt
```

> Your file now has **9 lines** total.

---

## Step 5 — Read the Full File

```bash
cat notes.txt
```

Expected output:

```
Linux file practice Day 06
Learning file read and write commands
Redirection operators are important
Logs are stored as text files
Configs are usually text based
tee writes and displays output together
cat reads complete files
head reads starting lines
tail reads ending lines
```

---

## Step 6 — Read Only the First 2 Lines

```bash
head -n 2 notes.txt
```

---

## Step 7 — Read Only the Last 2 Lines

```bash
tail -n 2 notes.txt
```

---

## Mini Runbook — Command Reference

| Command  | Purpose                        |
|----------|--------------------------------|
| `touch`  | Create an empty file           |
| `>`      | Write and overwrite            |
| `>>`     | Append new text                |
| `tee -a` | Display + append output        |
| `cat`    | Read entire file               |
| `head`   | Read top lines                 |
| `tail`   | Read bottom lines              |

---

## Real-World Use Cases

This exact workflow is used constantly in Linux troubleshooting for:

- Checking logs
- Editing configs
- Validating script outputs
- Debugging services
- Creating quick notes/runbooks
