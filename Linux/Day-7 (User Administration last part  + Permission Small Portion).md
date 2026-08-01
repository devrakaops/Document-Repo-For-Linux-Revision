
---

# Day-7: User Administration (A kind of Day-6 Part-4)

In this session, we continue learning **User Administration** by exploring some advanced configuration files that control how users are created and managed in Linux. After that, we learn how Linux records user activities, how to monitor login history, how the **sudo** mechanism works, and finally begin one of the most important Linux topics: **File Permissions**.

---

# Advanced User Configuration Files

Whenever we create a new user using the `useradd` command, Linux does not randomly decide where the home directory should be created, which shell should be assigned, or what password policies should be applied.

All these default settings are already stored inside configuration files.

Understanding these files is very important because system administrators often modify them to match company policies.

---

# 1. `/etc/default/useradd`

This file stores the **default settings** used by the `useradd` command whenever a new user is created.

To view the current configuration:

```bash
cat /etc/default/useradd
```

Example output:

```text
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

Let's understand each field.

| Field             | Purpose                                                 |
| ----------------- | ------------------------------------------------------- |
| GROUP             | Default primary group                                   |
| HOME              | Base location where home directories are created        |
| INACTIVE          | Number of inactive days after password expiration       |
| EXPIRE            | Account expiration date                                 |
| SHELL             | Default login shell                                     |
| SKEL              | Skeleton directory copied into every new home directory |
| CREATE_MAIL_SPOOL | Creates user mailbox                                    |

---

## Changing Default Home Directory

Normally Linux creates user home directories inside:

```text
/home
```

Suppose an organization has a separate disk mounted at:

```text
/mnt
```

and wants every new user's home directory there.

Open the file:

```bash
vi /etc/default/useradd
```

Change

```text
HOME=/home
```

to

```text
HOME=/mnt
```

Save the file.

Now create a user.

```bash
useradd fin
```

Check the home directory.

```bash
grep fin /etc/passwd
```

Output may look like

```text
fin:x:1005:1005::/mnt/fin:/bin/bash
```

Notice the home directory is now

```text
/mnt/fin
```

instead of

```text
/home/fin
```

This proves that `useradd` follows the configuration defined in `/etc/default/useradd`.

---

# 2. `/etc/login.defs`

This file contains **system-wide login defaults**.

View it:

```bash
cat /etc/login.defs
```

This file controls many important settings.

Examples include:

* Password aging
* UID ranges
* GID ranges
* Mail directory
* Password warning days
* Password expiration defaults

---

## Mail Directory

Linux provides each user with a mailbox.

Example:

```text
MAIL_DIR /var/mail
```

Each user gets a mail file inside:

```text
/var/mail/
```

Example

```text
/var/mail/student
```

---

## Password Warning Days

Example

```text
PASS_WARN_AGE 7
```

Meaning:

Before the password expires, Linux starts warning the user **7 days earlier**.

---

## Password Expiration

This file also defines defaults such as:

* Maximum password age
* Minimum password age
* Warning period

These become default values whenever a new account is created.

---

## UID Range

One of the most important settings is:

```text
UID_MIN
UID_MAX
```

Typical values:

```text
UID_MIN 1000
UID_MAX 60000
```

Meaning

Linux automatically assigns user IDs between

```text
1000 → 60000
```

for local users.

IDs below 1000 are generally reserved for:

* Root
* System users
* Service accounts

---

## GID Range

Similarly,

```text
GID_MIN
GID_MAX
```

defines the range of automatically assigned group IDs.

---

# 3. `/etc/security/limits.conf`

This file is extremely important in production environments.

Purpose:

It allows administrators to **limit system resources** that users or groups can consume.

Open the file:

```bash
vi /etc/security/limits.conf
```

---

## Why Do We Need It?

Imagine one user starts

* 10,000 processes
* Consumes all RAM
* Uses 100% CPU
* Opens thousands of files

Other users may not be able to work because the server resources become exhausted.

To prevent this, Linux allows administrators to define resource limits.

---

## Common Restrictions

Using `limits.conf`, we can restrict:

* Maximum login sessions
* Maximum running processes
* CPU usage
* Memory usage
* File size
* Open files
* Stack size

---

Example concept:

```text
student hard nproc 100
```

Meaning

The user **student** cannot run more than **100 processes**.

---

Another example:

```text
@student hard nproc 200
```

means every user belonging to the group **student** is limited to 200 processes.

---

This is a common security practice in multi-user Linux servers.

---

# AAA Security Concept (Audit)

Previously we learned AAA:

* Authentication
* Authorization
* Audit

Now we focus on the **Audit** part.

Audit means:

> Keeping records of user activities so administrators know who logged in, when they logged in, and what happened.

Linux provides several commands for auditing.

---

# 1. `w` Command

Syntax:

```bash
w
```

Example output:

```text
USER   TTY   FROM      LOGIN@   IDLE
root   tty1           10:20
john   pts/0 192.168.1.10
```

This command displays:

* Logged-in users
* Login time
* Idle time
* Source IP
* Running command

---

## TTY vs PTS

One common interview question is:

What is the difference between **TTY** and **PTS**?

### TTY

TTY means

**Physical Terminal**

Example:

Someone is sitting directly in front of the Linux machine using the keyboard and monitor.

Output:

```text
tty1
```

---

### PTS

PTS means

**Pseudo Terminal Slave**

This indicates a remote login.

Example:

SSH connection

```bash
ssh student@192.168.1.100
```

Output

```text
pts/0
```

So,

| Terminal | Meaning                               |
| -------- | ------------------------------------- |
| tty      | Local physical login                  |
| pts      | Remote login (SSH, terminal emulator) |

---

# 2. `last`

Command:

```bash
last
```

Purpose:

Displays the history of successful logins and logouts.

Example:

```text
student pts/0 192.168.1.20
```

You can see

* Username
* Login time
* Logout time
* Duration
* Source IP

This command is useful for checking who accessed the server.

---

# 3. `lastb`

Command:

```bash
lastb
```

Purpose:

Displays **failed login attempts**.

This command is especially useful for identifying:

* Wrong password attempts
* Unauthorized login attempts
* Brute-force attacks
* Security incidents

In production environments, administrators regularly check `lastb` to detect suspicious activity.

---

# Understanding Sudo

Normally, only the **root** user has permission to execute administrative commands.

For example:

```bash
useradd
```

```bash
passwd
```

```bash
systemctl restart
```

cannot be executed by an ordinary user.

However, giving everyone the root password is unsafe.

Linux solves this problem using **sudo**.

---

## What is Sudo?

Sudo stands for

**Super User Do**

It allows a normal user to temporarily execute commands with administrative privileges.

Instead of logging in as root, users simply prefix the command with:

```bash
sudo
```

Example:

```bash
sudo useradd test
```

---

# Wheel Group

Linux already contains a special administrative group called

```text
wheel
```

Members of this group are allowed to use `sudo`.

---

## Checking the Group

```bash
grep wheel /etc/group
```

Example output:

```text
wheel:x:10:
```

---

# Adding a User to the Wheel Group

Example:

```bash
usermod -aG wheel fin
```

Explanation:

* `-a` → Append
* `-G` → Supplementary group
* `wheel` → Administrative group
* `fin` → Username

Now the user `fin` becomes a sudo user.

---

# Sudo Authentication

When executing:

```bash
sudo useradd test
```

Linux asks:

```text
[sudo] password for fin:
```

Notice carefully:

Linux asks for **fin's password**, **not the root password**.

The system verifies that:

* the user belongs to the `wheel` group (or has another sudo rule), and
* the user knows **their own** password.

---

# The Sudo Lecture

The first time a user runs `sudo`, Linux displays a small security message.

Example:

```text
We trust you have received the usual lecture...
```

The message reminds users to:

* Respect the privacy of others.
* Think before executing commands.
* Use administrative privileges responsibly.

This message appears only the first time.

---

# Linux File Permissions

Linux is a multi-user operating system.

Therefore, every file and directory needs access control.

This is achieved using **permissions**.

View permissions using:

```bash
ls -l
```

Example:

```text
-rwxr-xr--
```

---

# Permission Structure

A typical permission string looks like:

```text
-rwxr-xr--
```

It can be divided into four parts:

```text
- | rwx | r-x | r--
```

| Part            | Meaning                                           |
| --------------- | ------------------------------------------------- |
| First character | File type (`-` for file, `d` for directory, etc.) |
| Owner           | Permissions for the file owner                    |
| Group           | Permissions for users in the file's group         |
| Others          | Permissions for everyone else                     |

---

# Three Basic Permissions

Linux uses three permission types:

| Symbol | Name    | Value |
| ------ | ------- | ----- |
| r      | Read    | 4     |
| w      | Write   | 2     |
| x      | Execute | 1     |

---

# Permission Meaning for Files

## Read (r)

Allows viewing file contents.

Example:

```bash
cat file.txt
```

---

## Write (w)

Allows modifying the file.

Example:

```bash
echo Hello >> file.txt
```

---

## Execute (x)

Allows running the file as a program or script.

Example:

```bash
./script.sh
```

---

# Permission Meaning for Directories

Permissions behave differently on directories.

## Read

Allows listing directory contents.

```bash
ls
```

---

## Write

Allows creating, deleting, or renaming files within the directory (provided other conditions such as directory ownership permit it).

---

## Execute

Allows entering or traversing the directory.

```bash
cd directory_name
```

Without execute permission on a directory, you cannot access it even if you know its name.

---

# Changing Permissions with `chmod`

Linux provides the `chmod` command to modify file and directory permissions.

There are two common methods:

1. Symbolic (alphabetic) method
2. Numeric (octal) method

---

# Symbolic Method

The symbolic method uses:

| Symbol | Meaning                     |
| ------ | --------------------------- |
| u      | User (Owner)                |
| g      | Group                       |
| o      | Others                      |
| a      | All (User + Group + Others) |
| +      | Add permission              |
| -      | Remove permission           |
| =      | Assign exact permission     |

Examples:

```bash
chmod o+w test
```

Add write permission for others.

```bash
chmod g-x test
```

Remove execute permission from the group.

```bash
chmod u+rwx test
```

Give the owner full permissions.

---

# Numeric Method

Each permission has a numeric value:

| Permission | Value |
| ---------- | ----- |
| Read       | 4     |
| Write      | 2     |
| Execute    | 1     |

The values are added together to create the required permission.

| Number | Permission | Calculation    |
| ------ | ---------- | -------------- |
| 7      | rwx        | 4+2+1          |
| 6      | rw-        | 4+2            |
| 5      | r-x        | 4+1            |
| 4      | r--        | 4              |
| 3      | -wx        | 2+1            |
| 2      | -w-        | 2              |
| 1      | --x        | 1              |
| 0      | ---        | No permissions |

Example:

```bash
chmod 754 test
```

Breakdown:

* **7 (Owner)** = `rwx` → Full control
* **5 (Group)** = `r-x` → Read and execute
* **4 (Others)** = `r--` → Read only

Result:

```text
-rwxr-xr--
```

---

# Key Takeaways

* `/etc/default/useradd` controls default settings used when creating new users.
* `/etc/login.defs` defines system-wide login defaults such as password policies and UID/GID ranges.
* `/etc/security/limits.conf` is used to restrict system resources for users and groups.
* The `w` command shows currently logged-in users and whether they are using a local (`TTY`) or remote (`PTS`) terminal.
* The `last` command displays successful login history, while `lastb` displays failed login attempts.
* `sudo` allows authorized users to execute administrative commands using **their own password** instead of the root password.
* Membership in the `wheel` group commonly grants sudo privileges.
* Linux permissions are divided into **Owner**, **Group**, and **Others**, with three basic permissions: **Read (r)**, **Write (w)**, and **Execute (x)**.
* The `chmod` command supports both **symbolic** (`u`, `g`, `o`, `+`, `-`, `=`) and **numeric** (4, 2, 1) methods for changing permissions.
