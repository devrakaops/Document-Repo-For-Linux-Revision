
---

# Linux Training – Day 5
<img width="1072" height="192" alt="image" src="https://github.com/user-attachments/assets/136f5d78-4685-4bfe-b396-a1a445dcd6a3" />

In the fifth session, the focus moves from basic file operations to some of the most powerful features of the Linux command line. The instructor explains different types of redirection, how multiple commands can be connected using pipelines, different ways to read files safely without editing them, and finally introduces the `grep` command for searching text inside files.

These concepts are extremely important because they are used regularly by Linux Administrators, DevOps Engineers, System Engineers, and Site Reliability Engineers (SREs).

---

# Understanding Redirection in Linux

Earlier, we learned that Linux commands either produce an output or an error. Instead of displaying these results on the terminal, Linux allows us to redirect them into files.

Redirection is commonly used while creating logs, storing command outputs, troubleshooting problems, and writing shell scripts.

There are four major types of redirection.

---

## 1. Output Redirection

Output redirection stores the successful output of a command into a file.

### Overwrite Output

```bash
command > filename
```

Example:

```bash
ls > files.txt
```

If `files.txt` already exists, its previous contents are removed and replaced with the new output.

---

### Append Output

```bash
command >> filename
```

Example:

```bash
date >> log.txt
```

Unlike `>`, this does not remove existing content.

Instead, the new output is added at the end of the file.

This is commonly used while maintaining log files.

---

## 2. Input Redirection

Input redirection allows a command to read its input from a file instead of waiting for keyboard input.

Syntax:

```bash
command < filename
```

Example:

```bash
sort < names.txt
```

The `sort` command reads the contents of `names.txt` as its input.

Although input redirection is not used as frequently as output redirection, it is very useful in scripting and automation.

---

## 3. Error Redirection

Every Linux command produces one of two results:

* Successful Output (Standard Output)
* Error Message (Standard Error)

Normal redirection (`>` or `>>`) only stores successful output.

It does **not** capture errors.

Linux treats errors separately.

To redirect only error messages, use:

### Overwrite Errors

```bash
command 2> error.log
```

Example:

```bash
ls abc 2> error.log
```

Since directory `abc` does not exist, the error message is written into `error.log` instead of appearing on the screen.

---

### Append Errors

```bash
command 2>> error.log
```

Example:

```bash
mkdir test
mkdir test 2>> errors.log
```

The second command generates an error because the directory already exists.

Instead of overwriting the file, the new error is appended.

---

## 4. Redirecting Both Output and Error Together

Sometimes a command produces both successful output and error messages.

In automation, we usually want to save everything into a single log file.

Linux provides:

```bash
command &> logfile
```

or

```bash
command &>> logfile
```

Example:

```bash
find / -name test &> result.log
```

The log file now contains:

* Successful search results
* Permission denied messages
* Other errors

Everything is stored together.

This makes troubleshooting much easier.

---

# Understanding Pipelines (`|`)

One of the greatest strengths of Linux is that commands can work together.

Instead of performing one large task with a single command, Linux encourages combining small commands.

The **pipe (`|`)** symbol connects multiple commands.

The output of the first command becomes the input of the second command.

General syntax:

```bash
command1 | command2
```

Think of it like a water pipe.

Water flows from one pipe into another.

Similarly,

```
Output of Command 1
        ↓
      Pipe (|)
        ↓
Input of Command 2
```

---

## Example

Suppose a file contains 100 lines.

You only want lines 11–15.

One solution is:

```bash
head -n 15 file.txt | tail -n 5
```

### Step 1

```bash
head -n 15 file.txt
```

Displays:

```
Line 1
Line 2
...
Line 15
```

---

### Step 2

The output is passed to:

```bash
tail -n 5
```

Which prints:

```
Line 11
Line 12
Line 13
Line 14
Line 15
```

Without pipes, achieving this would require multiple steps.

Pipelines make Linux commands simple, efficient, and powerful.

---

# Viewing Files Without Editing Them

System administrators often need to inspect configuration files or log files.

Opening these files in an editor increases the risk of accidentally modifying them.

Linux provides safe commands that only display file contents.

---

# The `head` Command

The `head` command displays the beginning of a file.

By default, it prints the first **10 lines**.

Example:

```bash
head file.txt
```

Output:

```
Line 1
Line 2
...
Line 10
```

---

### Display a Specific Number of Lines

```bash
head -n 20 file.txt
```

Displays the first 20 lines.

---

# The `tail` Command

The `tail` command displays the end of a file.

By default:

```bash
tail file.txt
```

Shows the last 10 lines.

---

### Display Last N Lines

```bash
tail -n 25 file.txt
```

Shows the last 25 lines.

---

### Why is `tail` Important?

System logs continuously grow.

Whenever a new event occurs, it is added at the bottom of the log file.

Instead of opening thousands of lines, administrators simply check the latest entries using:

```bash
tail /var/log/messages
```

or

```bash
tail /var/log/secure
```

This is one of the most commonly used Linux commands.

---

# The `less` Command

The `less` command opens a file in a scrollable view.

Example:

```bash
less file.txt
```

Features include:

* Scroll forward
* Scroll backward
* Search inside the file
* Exit safely without changing anything

---

### Search Inside `less`

Press:

```
/
```

Then type the word.

Example:

```
/root
```

The cursor jumps to the first matching result.

---

### Exit

Press:

```
q
```

to quit.

Unlike editors such as `vi`, `less` never modifies the file.

It is completely safe for reading important configuration files.

---

# The `more` Command

`more` is another file viewer.

Example:

```bash
more file.txt
```

It works similarly to `less` but has fewer features.

One useful feature is that it displays the percentage of the file that has been viewed.

Example:

```
--More--(45%)
```

Once the end of the file reaches **100%**, the command automatically exits.

Although `more` still exists, most Linux administrators prefer using `less` because it is more flexible.

---

# Searching Text Using `grep`

The `grep` command is one of the most powerful commands in Linux.

Its primary purpose is to search for specific words or patterns inside files.

Instead of manually reading hundreds or thousands of lines, `grep` quickly finds matching text.

General syntax:

```bash
grep "word" filename
```

Example:

```bash
grep root /etc/passwd
```

Output:

```
root:x:0:0:root:/root:/bin/bash
```

Only the matching line is displayed.

---

# Common `grep` Options

## Ignore Uppercase and Lowercase (`-i`)

Normally, `grep` is case-sensitive.

Example:

```bash
grep linux file.txt
```

Matches:

```
linux
```

But not:

```
Linux
LINUX
```

To ignore case:

```bash
grep -i linux file.txt
```

Now all variations are matched.

---

## Recursive Search (`-r`)

Instead of searching a single file, Linux can search an entire directory.

Example:

```bash
grep -r "password" /etc
```

Linux searches every file and subdirectory inside `/etc`.

The output includes the complete file path where the match was found.

This option is very useful when searching configuration files.

---

## Count Matches (`-c`)

Sometimes we only need to know how many lines contain the word.

Example:

```bash
grep -c root /etc/passwd
```

Instead of displaying matching lines, Linux displays only the count.

Example:

```
3
```

---

# Combining `grep` with Pipelines

One of the biggest advantages of Linux is combining `grep` with other commands.

Example:

```bash
head -n 20 /etc/passwd | grep root
```

Execution flow:

1. `head` reads the first 20 lines.
2. The output goes through the pipe (`|`).
3. `grep` searches only those 20 lines.
4. Matching lines are displayed.

This combination is widely used in Linux administration because it allows administrators to filter only the information they actually need.

---

# Summary

By the end of this session, students understood:

* The different types of redirection in Linux, including output, input, error, and combined output/error redirection.
* How the pipe (`|`) operator connects multiple commands by passing the output of one command as the input to another.
* How to safely read files using `head`, `tail`, `less`, and `more` without modifying them.
* How to search text efficiently using the powerful `grep` command.
* How `grep` can be combined with pipelines to filter command output, making Linux command-line operations faster, cleaner, and more efficient.

These commands form an essential part of everyday Linux administration and are used extensively in shell scripting, automation, DevOps workflows, log analysis, and troubleshooting.
