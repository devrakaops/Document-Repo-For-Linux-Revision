# Linux TAR — Archive Management

Another important Linux administration topic is:

```text
tar
```

`tar` stands for:

> **Tape Archive**

The name comes from older tape-based storage systems. Today, `tar` is commonly used to create, manage, and extract archives of files and directories.

---

# 1. Why Do We Need `tar`?

Suppose we have a directory containing many files:

```text
application/
├── file1
├── file2
├── file3
├── logs/
├── config/
└── scripts/
```

If we need to transfer this directory to another server, sending every file separately is not convenient.

Instead, we can combine the complete directory into one archive:

```text
application/
      ↓
application.tar
```

Now we have a single file that contains all the files and directories.

This single package is called an **archive**.

---

# 2. What Is an Archive?

An **archive** is a single file that contains multiple files and directories.

For example:

```text
file1
file2
file3
file4
   ↓
backup.tar
```

The archive keeps the files together in one package.

This makes it easier to:

* Store files
* Transfer files
* Create backups
* Distribute software
* Preserve directory structures

---

# 3. Archive vs Compression

It is very important to understand that **archiving and compression are different things**.

## Archiving

Archiving combines multiple files and directories into one file.

Example:

```text
10 files
   ↓
backup.tar
```

The main purpose is to package the files together.

---

## Compression

Compression reduces the size of data.

Example:

```text
28 MB
  ↓
6 MB
```

The main purpose is to save storage space and reduce the amount of data that needs to be transferred.

---

# 4. `tar` and Compression

`tar` mainly performs **archiving**.

However, `tar` can work together with compression tools such as:

```text
gzip
bzip2
xz
```

Therefore, we commonly use:

```text
tar + gzip
tar + bzip2
tar + xz
```

Examples:

```text
backup.tar
backup.tar.gz
backup.tar.bz2
backup.tar.xz
```

The extensions help us understand which compression method is being used.

---

# 5. Why Are TAR Archives Useful?

TAR archives are commonly used for several purposes.

## 5.1 Backup

We can package a complete directory into one archive.

Example:

```text
/etc
  ↓
backup.tar
```

This makes the directory easier to store or transfer.

---

## 5.2 Network Transfer

Suppose we have 1000 files:

```text
file1
file2
file3
...
file1000
```

Instead of transferring all files separately, we can create:

```text
backup.tar
```

and transfer one file.

This is much easier to manage.

---

## 5.3 Software Distribution

Source code and software packages are often distributed as TAR archives.

For example:

```text
application.tar.gz
```

The user can extract the archive and get the complete directory structure.

---

## 5.4 Storage

Multiple files and directories can be packaged into one archive.

This makes file management easier.

---

# 6. Check Directory Size Before Creating an Archive

Before creating an archive, it can be useful to check the size of the directory.

Use:

```bash
du -sh /etc
```

Example:

```text
28M    /etc
```

Here:

```text
du → disk usage
-s  → summary
-h  → human-readable
```

So:

```bash
du -sh /etc
```

means:

> Show the total disk usage of `/etc` in a human-readable format.

---

# 7. Using `du -sch`

Another useful command is:

```bash
du -sch /etc
```

The options are:

```text
-s → summary
-c → show grand total
-h → human-readable
```

This is useful when checking the size of multiple files or directories and their total size.

---

# 8. Basic TAR Syntax

The basic syntax of `tar` is:

```bash
tar [options] archive-name files/directories
```

Example:

```bash
tar -cvf backup.tar /etc
```

Let's understand the options:

```text
-c → create
-v → verbose
-f → specify archive file
```

Therefore:

```bash
tar -cvf backup.tar /etc
```

means:

> Create a TAR archive named `backup.tar` containing `/etc`.

---

# 9. Important TAR Options

| Option | Meaning                                    |
| ------ | ------------------------------------------ |
| `-c`   | Create an archive                          |
| `-x`   | Extract an archive                         |
| `-t`   | List archive contents                      |
| `-v`   | Show detailed output                       |
| `-f`   | Specify the archive filename               |
| `-C`   | Change to a directory before the operation |
| `-z`   | Use gzip compression                       |
| `-j`   | Use bzip2 compression                      |
| `-J`   | Use xz compression                         |

Easy way to remember:

```text
c → create
x → extract
t → list
v → verbose
f → file
z → gzip
j → bzip2
J → xz
```

---

# 10. Create a TAR Archive

To create a normal TAR archive:

```bash
tar -cvf backup.tar /etc
```

This creates:

```text
backup.tar
```

The archive contains the `/etc` directory and its contents.

The command can be understood as:

```text
-c → Create
-v → Show output
-f → Use backup.tar as the archive file
```

---

# 11. List the Contents of a TAR Archive

Before extracting an archive, we may want to see what is inside it.

Use:

```bash
tar -tvf backup.tar
```

Here:

```text
-t → list contents
-v → verbose output
-f → archive file
```

This allows us to inspect the files stored inside the archive without extracting them.

---

# 12. Extract a TAR Archive

To extract a normal TAR archive:

```bash
tar -xvf backup.tar
```

Here:

```text
-x → extract
-v → verbose
-f → archive file
```

The contents will be extracted into the current working directory.

---

# 13. Extract TAR to a Specific Directory

Sometimes we do not want to extract the archive into the current directory.

In that case, use the capital `-C` option.

Example:

```bash
tar -xvf backup.tar -C /tmp/
```

This extracts the archive into:

```text
/tmp/
```

---

## Important: `-c` vs `-C`

Do not confuse these two options.

```text
-c → create archive
-C → change directory
```

Linux commands are **case-sensitive**, so lowercase `c` and uppercase `C` have different meanings.

---

# 14. TAR with Gzip Compression

A normal TAR archive is not compressed.

We can combine TAR with **gzip** to reduce the archive size.

Use:

```bash
tar -czvf backup.tar.gz /etc
```

Here:

```text
-c → create
-z → gzip compression
-v → verbose
-f → archive filename
```

The result will be:

```text
backup.tar.gz
```

---

# 15. Extract a Gzip TAR Archive

To extract:

```bash
tar -xzvf backup.tar.gz
```

Here:

```text
-x → extract
-z → gzip
-v → verbose
-f → archive file
```

To extract it into a specific directory:

```bash
tar -xzvf backup.tar.gz -C /tmp/
```

---

# 16. TAR with Bzip2 Compression

Bzip2 compression is enabled using:

```text
-j
```

To create a Bzip2-compressed TAR archive:

```bash
tar -cjvf backup.tar.bz2 /etc
```

Here:

```text
-c → create
-j → bzip2 compression
-v → verbose
-f → archive filename
```

To extract:

```bash
tar -xjvf backup.tar.bz2
```

The important option is:

```text
-j → bzip2
```

---

# 17. TAR with Xz Compression

Xz compression is enabled using:

```text
-J
```

Notice that this is **capital `J`**.

To create an Xz-compressed archive:

```bash
tar -cJvf backup.tar.xz /etc
```

To extract:

```bash
tar -xJvf backup.tar.xz
```

The important option is:

```text
-J → xz
```

---

# 18. Compression Comparison

During the practical demonstration, the original `/etc` directory was approximately:

```text
28 MB
```

After creating archives with different compression methods, the approximate sizes were:

| Method          | TAR Option | Approx. Size |
| --------------- | ---------: | -----------: |
| Original `/etc` |          — |       ~28 MB |
| Gzip            |       `-z` |      ~6.2 MB |
| Bzip2           |       `-j` |      ~5.4 MB |
| Xz              |       `-J` |      ~4.4 MB |

The result can be understood as:

```text
Original
   ↓
~28 MB

Gzip
   ↓
~6.2 MB

Bzip2
   ↓
~5.4 MB

Xz
   ↓
~4.4 MB
```

In this particular example, **XZ produced the smallest archive**.

However, the smallest file size is not always the only thing we consider in a real production environment.

We may also need to consider:

```text
Compression Time
CPU Usage
Decompression Speed
Storage Requirement
Transfer Requirement
```

So, the correct compression method depends on the requirement.

---

# 19. TAR File Extensions

It is useful to understand common TAR file extensions.

| Extension  | Meaning                           |
| ---------- | --------------------------------- |
| `.tar`     | TAR archive without compression   |
| `.tar.gz`  | TAR archive compressed with gzip  |
| `.tar.bz2` | TAR archive compressed with bzip2 |
| `.tar.xz`  | TAR archive compressed with xz    |

Examples:

```text
backup.tar
backup.tar.gz
backup.tar.bz2
backup.tar.xz
```

---

# 20. Complete Practical Example

Let's create and manage a TAR archive step by step.

## Step 1: Check the directory size

```bash
du -sh /etc
```

---

## Step 2: Create a normal TAR archive

```bash
tar -cvf backup.tar /etc
```

---

## Step 3: Check the archive contents

```bash
tar -tvf backup.tar
```

---

## Step 4: Extract the archive

```bash
tar -xvf backup.tar
```

---

## Step 5: Create a gzip-compressed archive

```bash
tar -czvf backup.tar.gz /etc
```

---

## Step 6: Extract the gzip archive

```bash
tar -xzvf backup.tar.gz
```

---

## Step 7: Create a bzip2-compressed archive

```bash
tar -cjvf backup.tar.bz2 /etc
```

---

## Step 8: Extract the bzip2 archive

```bash
tar -xjvf backup.tar.bz2
```

---

## Step 9: Create an xz-compressed archive

```bash
tar -cJvf backup.tar.xz /etc
```

---

## Step 10: Extract the xz archive

```bash
tar -xJvf backup.tar.xz
```

---

# 21. TAR Command Cheat Sheet

### Create normal TAR

```bash
tar -cvf backup.tar /etc
```

### List contents

```bash
tar -tvf backup.tar
```

### Extract TAR

```bash
tar -xvf backup.tar
```

### Extract to a specific directory

```bash
tar -xvf backup.tar -C /tmp/
```

### Create Gzip archive

```bash
tar -czvf backup.tar.gz /etc
```

### Extract Gzip archive

```bash
tar -xzvf backup.tar.gz
```

### Create Bzip2 archive

```bash
tar -cjvf backup.tar.bz2 /etc
```

### Extract Bzip2 archive

```bash
tar -xjvf backup.tar.bz2
```

### Create Xz archive

```bash
tar -cJvf backup.tar.xz /etc
```

### Extract Xz archive

```bash
tar -xJvf backup.tar.xz
```

---

# 22. Important Points to Remember

1. `tar` stands for **Tape Archive**.

2. `tar` is mainly used to **create and manage archives**.

3. Archiving and compression are different concepts.

4. An archive combines multiple files/directories into one file.

5. Compression reduces the size of data.

6. `tar` can work together with:

```text
gzip
bzip2
xz
```

7. `-c` means **create**.

8. `-x` means **extract**.

9. `-t` means **list archive contents**.

10. `-v` means **verbose output**.

11. `-f` is used to specify the **archive filename**.

12. `-C` is used to specify the **directory for the operation**.

13. `-z` means **gzip**.

14. `-j` means **bzip2**.

15. `-J` means **xz**.

16. Linux options are case-sensitive, so:

```text
-c
```

and

```text
-C
```

have different meanings.

---

# 23. Final Revision

The complete TAR concept can be remembered like this:

```text
Files / Directories
        ↓
     tar
        ↓
     Archive
        ↓
 Optional Compression
        ↓
 ┌──────┼────────┐
 ↓      ↓        ↓
gzip   bzip2     xz
```

The most important commands are:

```bash
# Create
tar -cvf backup.tar /etc

# List
tar -tvf backup.tar

# Extract
tar -xvf backup.tar

# Gzip
tar -czvf backup.tar.gz /etc

# Bzip2
tar -cjvf backup.tar.bz2 /etc

# Xz
tar -cJvf backup.tar.xz /etc
```

### Easy Memory Trick

```text
c → Create
x → Extract
t → List
v → Verbose
f → File

z → gzip
j → bzip2
J → xz
```

> **Main idea:** `tar` packages files and directories into an archive, and it can be combined with compression methods such as gzip, bzip2, and xz to reduce the archive size.
