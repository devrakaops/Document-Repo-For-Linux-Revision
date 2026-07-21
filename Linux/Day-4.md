# Linux Training – Day 4

<img width="1068" height="192" alt="image" src="https://github.com/user-attachments/assets/6bba3e05-5c8a-475e-af1d-b0e29c42c060" />

---

# Learning Objectives

After completing this session, you should be able to:

* Understand the difference between absolute and relative paths.
* Navigate the Linux file system efficiently using path shortcuts.
* Copy, move, and rename files and directories.
* Count lines, words, bytes, and characters in files.
* Understand the information displayed by the `ls -l` command.
* Learn different Linux file types.
* Understand standard output (stdout) and output redirection.
* Use `echo` together with redirection to create and modify files.

---

# Understanding File Paths

Every file and directory in Linux has a unique location. A **path** tells Linux exactly where a file or directory is located.

Knowing how paths work is extremely important because almost every Linux command, shell script, and configuration file uses paths.

There are two types of paths in Linux:

* Absolute Path
* Relative Path

---

## Absolute Path

An **Absolute Path** is the complete address of a file or directory.

It always starts from the **root directory (`/`)**, which is the top-most directory of the Linux file system.

Since it always begins from the root, the location remains the same no matter where you are currently working.

### Example

```bash
/home/student/Documents/file.txt
```

Here,

* `/` → Root directory
* `home` → Home directory location
* `student` → User directory
* `Documents` → Folder
* `file.txt` → File

Because the complete path is provided, Linux can locate the file from anywhere.

---

## Relative Path

A **Relative Path** starts from your **current working directory** instead of the root directory.

It does **not** begin with `/`.

Suppose your current directory is:

```bash
/home/student
```

Instead of writing

```bash
/home/student/Documents/file.txt
```

you can simply write

```bash
Documents/file.txt
```

Linux assumes the path starts from your current location.

Relative paths are shorter and convenient when working inside the same directory structure.

---

# Home Directory Shortcut (`~`)

Linux provides a shortcut symbol:

```bash
~
```

The tilde (`~`) always represents the **home directory of the currently logged-in user**.

For example,

If the current user is

```text
student
```

then

```bash
~
```

represents

```bash
/home/student
```

---

## `cd`

The `cd` command changes your current directory.

### Return to Home Directory

```bash
cd
```

or

```bash
cd ~
```

Both commands take you directly to your home directory.

---

# Using Paths with Links

Earlier, we learned about **Hard Links** and **Soft Links**.

When creating links, Linux allows you to use **either absolute paths or relative paths**.

### Using an Absolute Path

```bash
ln -s /home/student/file1 /home/student/link1
```

### Using a Relative Path

```bash
cd /home/student
ln -s file1 link1
```

Both commands work correctly.

However, using an **absolute path** is generally recommended for symbolic links because the complete location of the original file is stored. If the symbolic link is moved to another directory, an absolute path is more likely to remain valid.

When a symbolic link points to a file that no longer exists or whose location can no longer be resolved, it becomes a **broken link**. Depending on your Linux distribution and terminal color settings, a broken link may appear in a different color (often red), but the color is not guaranteed and varies between systems.

---

# Word Count Command (`wc`)

The `wc` command stands for **Word Count**.

It is used to count different types of information inside a file.

Basic syntax:

```bash
wc filename
```

Example:

```bash
wc file.txt
```

Example output:

```text
10 45 350 file.txt
```

This means:

* 10 Lines
* 45 Words
* 350 Bytes

---

## Useful Options

Count only lines:

```bash
wc -l file.txt
```

Count only words:

```bash
wc -w file.txt
```

Count only bytes:

```bash
wc -c file.txt
```

Count only characters:

```bash
wc -m file.txt
```

---

# Copy Command (`cp`)

The `cp` command copies files or directories from one location to another.

Basic syntax:

```bash
cp source destination
```

Example:

```bash
cp file1 file2
```

This creates a copy of `file1` named `file2`.

---

## Copying Directories

To copy a directory, Linux needs to copy every file and subdirectory inside it.

For this purpose, use the **recursive (`-r`)** option.

```bash
cp -r project backup_project
```

The `-r` option means **recursive**, which tells Linux to copy the directory along with all of its contents.

Without `-r`, Linux cannot copy a directory.

---

# Move Command (`mv`)

The `mv` command is used to move files or directories from one location to another.

Syntax:

```bash
mv source destination
```

Example:

```bash
mv file1 /tmp/
```

The file is moved to the `/tmp` directory.

Unlike `cp`, the original file no longer remains at the source location after a successful move.

Unlike `cp`, the `mv` command does **not** require the `-r` option for moving directories.

---

## Renaming Files

The `mv` command is also used to rename files.

Example:

```bash
mv oldname.txt newname.txt
```

Here, the file is not copied. Only its name changes.

Similarly, directories can also be renamed.

---

# Understanding `ls -l`

The command

```bash
ls -l
```

(or the commonly used alias `ll` on many Linux distributions)

shows detailed information about files and directories.

Example output:

```text
-rw-r--r--. 1 student student 120 Jul 20 10:15 file.txt
```

Each part has a specific meaning.

---

## 1. File Type

The first character indicates the file type.

Common symbols are:

| Symbol | Meaning          |
| ------ | ---------------- |
| `-`    | Regular File     |
| `d`    | Directory        |
| `l`    | Symbolic Link    |
| `c`    | Character Device |
| `b`    | Block Device     |
| `p`    | Named Pipe       |
| `s`    | Socket           |

Although beginners mostly work with regular files, directories, and symbolic links, Linux supports several file types.

---

## 2. File Permissions

The next nine characters represent permissions.

Example:

```text
rw-r--r--
```

Permissions are divided into three groups:

* Owner
* Group
* Others

Each group has three possible permissions:

* `r` → Read
* `w` → Write
* `x` → Execute

These permissions determine who can access or modify the file.

---

## 3. Extended Security Attributes

Sometimes you may notice an extra character after the permission bits.

Example:

```text
-rw-r--r--.
```

or

```text
-rw-r--r--+
```

* A **`.`** commonly indicates extended security attributes. On RHEL-based systems (such as Red Hat, Rocky Linux, or CentOS), these are often related to **SELinux**.
* A **`+`** usually indicates that the file has **Access Control Lists (ACLs)** configured.

The exact meaning depends on the Linux distribution and its security configuration.

---

## 4. Link Count

The number after the permissions represents the number of hard links associated with the file or directory.

Example:

```text
1
```

---

## 5. Owner

This shows the user who owns the file.

Example:

```text
student
```

---

## 6. Group Owner

This shows the group that owns the file.

Example:

```text
student
```

---

## 7. File Size

The next number shows the file size in bytes.

Example:

```text
120
```

---

## 8. Last Modified Date and Time

Linux displays when the file was last modified.

Example:

```text
Jul 20 10:15
```

---

## 9. File Name

Finally, Linux displays the name of the file or directory.

Example:

```text
file.txt
```

---

# Introduction to Redirection

Normally, when you execute a command, its output is displayed on the terminal screen. This output is called **Standard Output (stdout)**.

Sometimes, instead of displaying the output on the screen, you may want to save it in a file. This is where **redirection** is used.

---

# The `echo` Command

The `echo` command prints text or the value of variables.

Examples:

```bash
echo "Hello Linux"
```

```bash
echo $HOME
```

The output is normally displayed on the terminal.

---

# Overwrite Redirection (`>`)

The `>` operator redirects the output of a command to a file.

If the file does not exist, Linux creates it.

If the file already exists, its previous contents are **deleted and replaced** with the new output.

Example:

```bash
echo "Linux" > file.txt
```

After running this command, `file.txt` contains only:

```text
Linux
```

If `file.txt` already had any previous content, it is overwritten.

---

# Append Redirection (`>>`)

The `>>` operator also redirects output to a file, but instead of replacing existing content, it **adds** the new output at the end of the file.

Example:

```bash
echo "DevOps" >> file.txt
```

If `file.txt` already contains:

```text
Linux
```

After executing the command, it becomes:

```text
Linux
DevOps
```

This is useful when you want to keep existing data and continuously add new information.

---

# Summary

In this session, we learned how Linux uses **absolute and relative paths** to locate files and directories, and how the **home directory shortcut (`~`)** simplifies navigation. We explored how links can be created using either type of path and why absolute paths are often preferred for symbolic links.

We then studied important file management commands such as **`wc`**, **`cp`**, and **`mv`**, learning how to count file contents, copy files and directories, and move or rename files.

Next, we examined the detailed output of **`ls -l`**, understanding file types, permissions, ownership, security attributes, timestamps, and other metadata displayed by Linux.

Finally, we introduced **standard output (stdout)** and learned how to redirect command output using **`>`** to overwrite a file or **`>>`** to append data, along with practical usage of the **`echo`** command. These concepts form the foundation for creating files, managing content, and automating tasks in Linux.
