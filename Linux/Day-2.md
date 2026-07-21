
---

# Linux Training – Day 2
<img width="1069" height="196" alt="image" src="https://github.com/user-attachments/assets/65ae37d4-ab10-45c2-9206-2d8a349c4a06" />

# Linux Navigation, Users, File System Hierarchy & Basic Commands

---

# Learning Objectives

After completing this session, you should be able to:

* Log in to a Linux machine.
* Understand the difference between GUI and CLI.
* Understand different types of Linux users.
* Understand the Linux File System Hierarchy (FSH).
* Navigate between directories.
* Understand the terminal prompt.
* Execute basic Linux commands.
* Create and remove files and directories.
* Check system information.

---

# Introduction

In the previous session, we learned about Linux, its history, distributions, and installation.

Now that Linux has been installed successfully, the next step is learning how to use it.

A Linux administrator or DevOps Engineer spends most of their daily work inside the Terminal. Therefore, before learning advanced topics like users, permissions, networking, Docker, Kubernetes, or automation, we must become comfortable with the Linux command line.

This session focuses on understanding the Linux environment and learning the most commonly used commands.

---

# Logging into Linux

After installing Linux, the first screen that appears is the Login Screen.

Normally, Linux allows two ways to log in.

## 1. Local User Login

This is the user account created during Linux installation.

Example:

```
Username : student
Password : ********
```

This user has limited permissions and is used for normal daily work.

---

## 2. Root User Login

Linux also has a special administrative account called **root**.

Some Linux distributions do not show the root user on the login screen.

Instead, there is an option called

```
Not Listed
```

After clicking this option, you can manually enter

```
Username : root
Password : ********
```

---

# Understanding GUI and CLI

Once logged in, Linux displays the Desktop Environment (GUI).

GUI stands for

> Graphical User Interface

Examples of GUI applications include:

* Firefox Browser
* File Manager
* Terminal
* Software Manager
* Settings
* Calculator
* Bluetooth
* Network Settings
* Display Settings

Everything can be accessed using icons and mouse clicks.

---

Although Linux provides a GUI, DevOps Engineers rarely perform administration through graphical tools.

Instead, almost all server administration happens through the Terminal.

---

# What is CLI?

CLI stands for

> Command Line Interface

Instead of clicking icons, we type commands.

Example:

```
date
```

or

```
pwd
```

Linux executes the command and immediately returns the result.

CLI is

* Faster
* Lightweight
* Easier to automate
* Consumes fewer resources
* Used on remote servers
* Preferred by System Administrators
* Preferred by DevOps Engineers

---

# Opening the Terminal

The Terminal application allows us to communicate with Linux.

When Terminal opens, Linux displays something similar to

```
[root@server ~]#
```

At first, this looks confusing.

But every part has a meaning.

---

# Understanding the Linux Prompt

Example

```
[root@server ~]#
```

Break it into pieces.

```
root
```

Current logged-in user.

---

```
server
```

Hostname of the machine.

---

```
~
```

Current location.

The tilde (`~`) represents the current user's Home Directory.

For root user,

```
~
```

means

```
/root
```

For a local user named john,

```
~
```

means

```
/home/john
```

---

Finally,

```
#
```

indicates that the current user is Root.

If the prompt ends with

```
$
```

then the current user is a Local User.

Example

Root

```
[root@server ~]#
```

Local User

```
[john@server ~]$
```

This is one of the quickest ways to identify whether you have administrative privileges.

---

# Types of Linux Users

Linux divides users into three major categories.

---

## 1. Root User

Root is also known as

* Super User
* Administrator

The root user has complete control over the operating system.

Root can

* Install software
* Remove software
* Create users
* Delete users
* Manage services
* Change system settings
* Modify any file
* Shut down the system

Simply remember:

> Root can perform everything inside Linux.

---

## 2. Local User

A Local User is created for everyday work.

Examples:

```
john
```

```
developer
```

```
student
```

```
rakesh
```

Unlike root, a local user has limited permissions.

A local user normally works only inside their own home directory.

Example

```
/home/john
```

This improves system security because one user cannot accidentally modify another user's files or important operating system files.

---

## 3. System User

Linux automatically creates many users for applications and internal services.

Examples include users for:

* shutdown
* mail
* ftp
* nginx
* apache
* mysql
* postgres

These users are not meant for human login.

They exist only to run background services securely.

Whenever you install software, Linux may automatically create a dedicated system user for that application.

---

# Understanding the Linux File System

One of the most important concepts in Linux is

> Everything is treated as a file.

Examples include:

* Normal files
* Directories
* Hard disks
* Pen drives
* Printers
* Keyboard
* Mouse
* Network devices

Linux represents almost everything as files.

---

# File System Hierarchy (FSH)

Linux follows a tree structure.

---
<img width="1495" height="704" alt="Gemini_Generated_Image_1wl5wf1wl5wf1wl5" src="https://github.com/user-attachments/assets/17343b50-78bb-49e8-8316-fed3bf0462dc" />

---


At the very top is

```
/
```

called the

* Root Directory
* Master Directory

Everything inside Linux starts from this directory.

Nothing exists outside `/`.

A simplified structure looks like this:

```text
                /
     -------------------------
     |   |   |   |   |   |
   boot etc home usr var tmp
```

Every directory has a specific purpose.

---

# /boot

Contains files required during system startup.

Examples include:

* Kernel
* Bootloader files
* Initial RAM disk

Without `/boot`, Linux cannot start properly.

---

# /dev

The word **dev** means

```
Devices
```

Linux stores device files here.

Examples:

* Hard Disk
* SSD
* USB Drive
* CD/DVD
* Keyboard
* Mouse
* Printer

Whenever new hardware is connected, Linux creates device files inside `/dev`.

---

# /etc

This directory stores configuration files.

Almost every application's configuration exists here.

Examples:

* Network configuration
* DNS configuration
* User configuration
* SSH configuration
* Service configuration

Whenever we change Linux settings, we often modify files inside `/etc`.

---

# /home

Every local user receives a personal directory inside `/home`.

Example

```
/home/student
```

```
/home/rakesh
```

```
/home/developer
```

Users usually store their documents, scripts, projects, and personal files here.

---

# /root

Many beginners think

```
/root
```

and

```
/
```

are the same.

They are different.

```
/
```

is the Root Directory of the entire Linux file system.

```
/root
```

is simply the Home Directory of the root user.

---

# /usr

Most installed software resides here.

It contains application binaries, libraries, documentation, and shared resources.

Two important directories are:

```
/usr/bin
```

Contains common commands used by all users.

Examples:

* ls
* pwd
* cat
* cp
* mv

Another directory is

```
/usr/sbin
```

This contains administrative commands mainly used by system administrators.

Examples:

* useradd
* reboot
* shutdown

---

# /var

The word **var** means

```
Variable
```

It stores files whose contents keep changing.

Examples:

* Log files
* Cache
* Mail queues
* Databases
* Print queues

One important directory is

```
/var/log
```

where Linux stores system logs.

---

# /tmp

Temporary files are stored here.

Applications use this directory while running.

Linux automatically cleans unused temporary files after a certain period (commonly around 10 days on many systems, depending on the distribution and its cleanup policy).

This prevents unnecessary storage consumption.

---

# Understanding the Shell

The Shell is a program that accepts user commands and communicates with the Linux Kernel.

The flow is:

```text
User

   ↓

Shell (Bash)

   ↓

Kernel

   ↓

Hardware
```

The Shell acts as an interpreter.

It reads your command, understands it, and asks the Kernel to perform the requested operation.

The default shell in most Linux distributions is

```
Bash
```

which stands for

```
Bourne Again Shell
```

---

# Basic Linux Commands

## whoami

Displays the currently logged-in user.

Syntax

```bash
whoami
```

Example

```bash
[root@server ~]# whoami
root
```

---

## hostname

Displays the hostname of the system.

```bash
hostname
```

Example

```
server
```

---

## pwd

PWD stands for

> Present Working Directory

It shows your current location.

Example

```bash
pwd
```

Output

```
/root
```

---

## ls

Lists files and directories.

```bash
ls
```

Example

```
file1
notes
script.sh
```

---

## ll

Displays detailed information.

```bash
ll
```

It shows:

* Permissions
* Owner
* Group
* Size
* Date
* File Name

This command is frequently used by Linux administrators.

---

## cd

Changes the current directory.

Example

```bash
cd /etc
```

Check the location:

```bash
pwd
```

Output

```
/etc
```

---

Return to the Home Directory

```bash
cd
```

or

```bash
cd ~
```

---

Move back one directory

```bash
cd ..
```

Example

Current location

```
/home/student/Documents
```

After

```bash
cd ..
```

New location

```
/home/student
```

---

## mkdir

Creates a new directory.

Syntax

```bash
mkdir directory_name
```

Example

```bash
mkdir project
```

Verify

```bash
ls
```

Output

```
project
```

---

## touch

Creates an empty file.

Example

```bash
touch notes.txt
```

Verify

```bash
ls
```

Output

```
notes.txt
```

---

## rm

Removes a file.

Example

```bash
rm notes.txt
```

The file is permanently deleted.

Use this command carefully.

---

## rmdir

Removes an empty directory.

Example

```bash
rmdir project
```

If the directory contains files, Linux will display an error and the directory will not be removed.

---

## date

Displays the current system date and time.

```bash
date
```

Example Output

```
Tue Jul 21 11:20:05 IST 2026
```

---

## cal

Displays the calendar.

```bash
cal
```

Example Output

```
     July 2026
Su Mo Tu We Th Fr Sa
```

---

## history

Displays previously executed commands.

```bash
history
```

This is useful when you want to repeat an old command without typing it again.

---

## clear

Clears the terminal screen.

```bash
clear
```

Shortcut

```
Ctrl + L
```

---

# hostnamectl

The `hostnamectl` command is used to display detailed information about the operating system and manage the system hostname.

View system information:

```bash
hostnamectl
```

Example output includes:

* Static Hostname
* Operating System
* Kernel Version
* Architecture

To change the hostname:

```bash
hostnamectl set-hostname devops-server
```

The hostname is updated immediately, but the terminal prompt may still show the old hostname.

To refresh the current shell, run:

```bash
bash
```

Now the prompt will display the new hostname.

Example:

Before:

```text
[root@server ~]#
```

After changing the hostname:

```bash
hostnamectl set-hostname devops-server
bash
```

New prompt:

```text
[root@devops-server ~]#
```

---

# Summary

In this session, we learned:

* How to log in as a local user and as the root user.
* The difference between GUI and CLI, and why DevOps Engineers primarily use the Terminal.
* The three types of Linux users: Root, Local, and System.
* The Linux File System Hierarchy and the purpose of important directories such as `/boot`, `/etc`, `/home`, `/root`, `/usr`, `/var`, and `/tmp`.
* How to understand the Linux terminal prompt.
* The role of the Bash shell as an interface between the user and the Linux Kernel.
* Essential Linux commands for identifying the current user, navigating directories, managing files and directories, checking system information, and viewing command history.
* How to use `hostnamectl` to view system information and change the system hostname.

These concepts form the foundation for all future Linux administration tasks and will be used repeatedly throughout Docker, Kubernetes, Ansible, and DevOps practical sessions.
