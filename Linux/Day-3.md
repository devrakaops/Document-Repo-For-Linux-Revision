
---

# Linux Training – Day 3
<img width="1072" height="198" alt="image" src="https://github.com/user-attachments/assets/c52a3f70-8b65-4257-b38d-0091402bca8e" />

---

# Learning Objectives

After completing this session, you should be able to:

* Understand how Linux command options work.
* Learn how to get help for any Linux command.
* Understand Linux case sensitivity and space sensitivity.
* Create and remove files and directories efficiently.
* Use recursive operations on directories.
* View file contents using different commands.
* Understand the purpose of the Tree command.
* Learn different Linux text editors.
* Work confidently with the Vim editor.
* Understand Linux Inodes.
* Create and manage Soft Links and Hard Links.
* Differentiate between Soft Links and Hard Links.

---

# Revising Previous Session

In the previous session, we learned:

* Linux directory structure
* Absolute and Relative paths
* Creating files and directories
* Copying and moving files
* Renaming files
* Removing files
* Basic navigation commands

Now we move one step further and learn how professionals work with files and editors inside Linux.

---

# Understanding Command Options

Until now we have been using commands like:

```bash
ls
mkdir
rm
cp
mv
```

But every Linux command can perform multiple tasks.

Those additional behaviors are controlled using **Options**.

Options are generally written using a hyphen (`-`).

Example:

```bash
ls -a
```

Here,

* `ls` is the command
* `-a` is an option

The option changes the behavior of the command.

---

# Why Do We Need Options?

Suppose we simply execute:

```bash
ls
```

Output:

```
Documents
Downloads
Desktop
```

Hidden files are not shown.

Now execute:

```bash
ls -a
```

Output:

```
.
..
.bashrc
.profile
Documents
Downloads
Desktop
```

Now Linux also displays hidden files.

So the command is the same.

Only the option changes.

---

# Common Options Used with ls

## 1. ls

Lists files and directories.

```bash
ls
```

---

## 2. ls -a

Displays all files including hidden files.

Hidden files start with a dot (`.`).

Example:

```
.bashrc
.profile
.git
```

---

## 3. ls -l

Displays information in long format.

```bash
ls -l
```

Example output

```
-rw-r--r-- 1 root root 1024 Jul 21 file1
```

This output contains:

* File permission
* Number of links
* Owner
* Group
* File size
* Modified date
* File name

Later we will study every column in detail.

---

## 4. ls -la

Both options can be combined.

```bash
ls -la
```

This displays

* Hidden files
* Long listing

at the same time.

---

# Linux is Case Sensitive

Linux treats uppercase and lowercase letters as completely different.

Example

```bash
mkdir Test
mkdir test
```

These create two different directories.

```
Test
test
```

Both can exist together.

Similarly,

```bash
ABC.txt
abc.txt
Abc.txt
```

are three different files.

Always remember:

Linux is **100% case-sensitive**.

---

# Linux is Space Sensitive

Linux also treats spaces very carefully.

Example

Correct:

```bash
mkdir DevOps
```

Wrong:

```bash
mk dir DevOps
```

Linux now thinks:

```
mk
```

is one command

and

```
dir
```

is another argument.

It produces an error.

Similarly,

```
my file.txt
```

contains a space.

Linux treats it as two different words unless enclosed in quotes.

Correct:

```bash
touch "my file.txt"
```

or

```bash
touch my\ file.txt
```

---

# Getting Help in Linux

Nobody can remember every command.

Linux provides built-in help.

---

# Method 1 — Using --help

Example

```bash
ls --help
```

Displays:

* Syntax
* Options
* Examples

Similarly,

```bash
mkdir --help
```

or

```bash
cp --help
```

This is useful for quick reference.

---

# Method 2 — Using man Command

Linux provides detailed manuals.

Syntax

```bash
man command_name
```

Example

```bash
man ls
```

This opens the manual page.

The manual includes:

* Description
* Syntax
* Options
* Examples
* Related commands

---

## Navigation inside man

Move down

```
Arrow Down
```

Move up

```
Arrow Up
```

Search

```
/keyword
```

Quit

```
q
```

---

# Removing Directories Recursively

Previously we learned:

```bash
rmdir directory
```

But it works only for empty directories.

Example

```
demo/
    file1
```

Now execute

```bash
rmdir demo
```

Output

```
Directory not empty
```

---

Instead use

```bash
rm -r demo
```

Option

```
-r
```

means

**Recursive**

Linux removes

* Directory
* Files
* Subdirectories

everything together.

---

# Verbose Mode

Sometimes we want to know exactly what Linux is deleting.

Use

```bash
rm -rv demo
```

Option

```
-v
```

means

Verbose

Output

```
removed file file1
removed directory demo
```

Now Linux shows every action.

---

# Force Remove

Sometimes Linux asks

```
Remove this file?
```

To skip confirmation

```bash
rm -rf demo
```

Here

```
-f
```

means

Force.

Linux deletes everything immediately.

⚠ **Be extremely careful with `rm -rf`**, especially when working as the `root` user. Once deleted, files generally cannot be recovered easily.

---

# Creating Multiple Directories Together

Normally

```bash
mkdir dir1
mkdir dir2
mkdir dir3
```

But Linux provides a faster method.

```bash
mkdir -p dir1/dir2/dir3
```

Linux automatically creates

```
dir1
 └── dir2
      └── dir3
```

Even if parent directories do not already exist.

---

# Viewing File Contents

---

# cat Command

Syntax

```bash
cat filename
```

Example

```bash
cat notes.txt
```

Displays the complete file content on the terminal.

Useful for small files.

---

# Limitations of cat

If a file contains

1000 lines

then

```bash
cat
```

prints all 1000 lines immediately.

Reading becomes difficult.

For large files, commands such as `less` or `more` are usually preferred (these will be covered later).

---

# Tree Command

Sometimes we need to visualize the directory structure.

Example

```
Project
    Docs
    Images
    Source
```

Instead of repeatedly using

```bash
ls
```

use

```bash
tree
```

Output

```
Project
├── Docs
├── Images
└── Source
```

This displays the hierarchy in a graphical tree format.

It becomes very useful while working on projects.

---

# Linux Text Editors

Linux offers multiple ways to edit files.

The session introduces four common editors.

---

# 1. gedit

Graphical editor.

Similar to Windows Notepad.

Open using:

```bash
gedit file.txt
```

Characteristics:

* GUI-based
* Easy for beginners
* Requires a graphical desktop
* Rarely used on production servers because most Linux servers do not have a GUI installed.

---

# 2. nano

Command Line Editor.

Open:

```bash
nano file.txt
```

Characteristics:

* Simple
* Menu shortcuts shown at the bottom
* Easier than Vim for beginners

However, many enterprise environments prefer Vim.

---

# 3. vi

Classic Linux editor.

Available on almost every Linux distribution.

Lightweight and reliable.

---

# 4. vim

Vim means

**Vi Improved**

It is an enhanced version of the original `vi`.

Additional features include:

* Syntax highlighting
* Better navigation
* Undo history
* Search improvements
* Plugins
* Better editing experience

This is one of the most widely used editors by Linux administrators and DevOps engineers.

---

# Understanding Vim Modes

Unlike Notepad,

Vim works in different modes.

---

## Command Mode

Default mode.

Used for

* Navigation
* Copy
* Delete
* Paste
* Save
* Exit

You cannot type normal text here.

---

## Insert Mode

Press

```
i
```

Now you can type normally.

---

## Returning to Command Mode

Press

```
Esc
```

This exits Insert Mode and returns to Command Mode.

---

# Saving and Exiting Vim

Save

```bash
:w
```

Save and Quit

```bash
:wq
```

Quit

```bash
:q
```

Quit without saving

```bash
:q!
```

---

# Useful Vim Shortcuts

Copy current line

```
yy
```

Paste

```
p
```

Delete current line

```
dd
```

Undo

```
u
```

These shortcuts significantly improve editing speed once you become comfortable with Vim.

---

# Learning Vim

Linux provides a built-in tutorial.

Run

```bash
vimtutor
```

It teaches Vim step by step using practical exercises.

Anyone who wants to master Vim should complete this tutorial.

---

# Understanding Inodes

Before learning links, we must understand **Inodes**.

Every file stored in Linux has an **Inode Number**.

An inode stores metadata about the file, such as:

* File permissions
* Owner
* Group
* File size
* Time stamps
* Location of the data blocks on disk

**Important:** The inode does **not** store the file name. The file name is stored separately in the directory entry, which points to the inode.

To display inode numbers, use:

```bash
ls -i
```

Example:

```
45873 file1.txt
```

Here, `45873` is the inode number of `file1.txt`.

---

# Linux Links

Linux provides two types of links:

1. Soft Link (Symbolic Link)
2. Hard Link

Although both allow multiple names to access data, they work differently.

---

# Soft Link (Symbolic Link)

A Soft Link behaves like a Windows shortcut.

Create a soft link:

```bash
ln -s original.txt shortcut.txt
```

Here:

* `original.txt` is the original file.
* `shortcut.txt` is the symbolic link.

Characteristics:

* Has its own inode number.
* Points to the original file.
* Occupies only a small amount of disk space to store the path.
* Can link files or directories.
* Can cross different file systems.

If the original file is deleted, the symbolic link becomes broken (also called a **dangling link**) because it points to a file that no longer exists.

---

# Hard Link

Create a hard link:

```bash
ln original.txt copy.txt
```

Characteristics:

* Shares the same inode number as the original file.
* Both names point to the same data blocks on disk.
* No duplicate copy of the file data is created.
* Deleting one name does **not** remove the data as long as another hard link still exists.

This makes hard links very space-efficient.

**Limitations of Hard Links:**

* Can only be created for files (not directories, in normal usage).
* Cannot span different file systems or partitions.

---

# Verifying Hard Links

Create a file:

```bash
touch data.txt
```

Create a hard link:

```bash
ln data.txt data_copy.txt
```

Check inode numbers:

```bash
ls -i
```

Example output:

```
48219 data.txt
48219 data_copy.txt
```

Both files have the **same inode number**, confirming they are hard links to the same data.

---

# Verifying Soft Links

Create a symbolic link:

```bash
ln -s data.txt data_link.txt
```

Check inode numbers:

```bash
ls -i
```

Example:

```
48219 data.txt
51782 data_link.txt
```

The inode numbers are different because the symbolic link is a separate file that points to the original.

---

# Soft Link vs Hard Link

| Feature                   | Soft Link           | Hard Link                      |
| ------------------------- | ------------------- | ------------------------------ |
| Similar to                | Windows Shortcut    | Another name for the same file |
| Inode                     | Different           | Same                           |
| Original file deleted     | Link becomes broken | File remains accessible        |
| Can link directories      | Yes                 | Generally No                   |
| Works across file systems | Yes                 | No                             |
| Storage usage             | Very small          | No additional data blocks used |

---

# Commands Practiced in This Session

```bash
ls
ls -a
ls -l
ls -la

ls --help
man ls

rm -r
rm -rv
rm -rf

mkdir -p

cat

tree

gedit
nano
vi
vim

:w
:wq
:q
:q!

yy
dd
p
u

vimtutor

ls -i

ln
ln -s
```

---

# Session Summary

In this session, we moved beyond basic file and directory operations and explored more advanced Linux command usage. We learned how command options modify behavior, how to use `--help` and `man` to discover command documentation, and why Linux is strictly case-sensitive and space-sensitive.

We practiced recursive directory operations using `rm -r`, `rm -rf`, and `mkdir -p`, viewed file contents with `cat`, and displayed directory structures using `tree`.

The session also introduced the major Linux text editors, with a strong focus on **Vim**, covering its modes, essential commands, and common shortcuts used in day-to-day administration.

Finally, we learned how Linux stores files using **inodes**, and how **Soft Links** and **Hard Links** work internally, including their differences, advantages, limitations, and practical verification using `ls -i`.

By the end of Day 3, students should be comfortable using advanced Linux commands, editing files with Vim, and understanding how Linux links files at the filesystem level—fundamental skills for Linux administration, DevOps, and RHCSA preparation.
