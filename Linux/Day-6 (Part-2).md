---

# Day-6 (Part-2) – Linux User and Group Administration

---

# Understanding the `/etc/group` File

Just as `/etc/passwd` stores user information, Linux maintains another important file called **`/etc/group`**, which stores information related to groups.

To view its contents:

```bash
cat /etc/group
```

Every line in this file represents one group and consists of **four colon (`:`) separated fields**.

Example:

```text
developers:x:1002:user1,user2
```

Structure:

```text
GroupName:Password:GID:Members
```

---

## Field 1 – Group Name

The first field contains the **name of the group**.

Example:

```text
developers
```

This group might have been created using:

```bash
groupadd developers
```

Linux uses this name whenever users or permissions refer to the group.

---

## Field 2 – Group Password

Example:

```text
x
```

Historically, Linux allowed passwords for groups.

Modern Linux systems rarely use this feature.

Therefore, this field usually contains:

```text
x
```

or sometimes remains empty.

For practical administration, this field is generally ignored.

---

## Field 3 – Group ID (GID)

Example:

```text
1002
```

Every group has a unique **Group ID (GID)**.

Just like users are identified internally by UID, groups are identified internally using GID.

Linux actually works with numbers internally.

Whenever permissions are checked, Linux compares the **UID** and **GID**, not the names.

---

## Field 4 – Group Members

Example:

```text
user1,user2,user3
```

This field stores the members of the group.

However, this is where many beginners become confused.

This field **does not show every user belonging to the group.**

It only shows **Secondary Group Members**.

---

# Primary Group vs Secondary Group

Every Linux user must have **one Primary Group**.

A user may also belong to **multiple Secondary Groups**.

Suppose we create a user:

```bash
useradd rakesh
```

Linux automatically creates

* User: `rakesh`
* Group: `rakesh`

This group becomes the user's **Primary Group**.

Now check:

```bash
cat /etc/group
```

You may see:

```text
rakesh:x:1005:
```

Notice something important.

The member list is empty.

Many beginners think:

> "The user does not belong to this group."

This is incorrect.

The user **is** a member.

But because it is the **Primary Group**, Linux never lists it in the fourth field.

This field only displays **Secondary Group Members**.

---

# How to Check the Primary Group

Instead of `/etc/group`, use:

```bash
id username
```

Example:

```bash
id rakesh
```

Output:

```text
uid=1005(rakesh)
gid=1005(rakesh)
groups=1005(rakesh),1006(developers)
```

Here:

Primary Group:

```text
gid=1005(rakesh)
```

Secondary Group:

```text
developers
```

You can also verify the primary group using:

```bash
grep rakesh /etc/passwd
```

---

# Why Primary Group Matters

Whenever a user creates a new file,

Linux automatically assigns

* Owner → User
* Group → Primary Group

For example:

```bash
touch demo.txt
```

Checking ownership:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 rakesh developers demo.txt
```

The group attached to the file is the user's **Primary Group**.

---

# Switching Between Users

Linux provides the **Switch User** command:

```bash
su
```

The instructor explained that many students use it incorrectly.

There are two commonly used methods.

---

# Method 1

```bash
su username
```

Example:

```bash
su rakesh
```

This changes to the target user's shell.

However, it **does not perform a complete login**.

You continue using much of the previous user's environment.

Examples include:

* Previous environment variables
* Previous PATH settings
* Previous HOME references (in some cases)
* Previous shell history behavior

This is why this method is generally avoided.

---

# Method 2 (Recommended)

```bash
su - username
```

Example:

```bash
su - rakesh
```

The hyphen (`-`) makes a huge difference.

Linux performs a **complete login session**.

It loads everything associated with the target user.

Examples include:

* User environment variables
* Login profile
* Home directory
* User-specific shell configuration
* Login shell
* Last login record

This behaves almost exactly as if the user logged in directly.

For administration work, this is considered the correct approach.

---

# Password Requirement While Switching Users

The instructor clarified an important rule.

## Root User

Root can switch into any account.

Example:

```bash
su - rakesh
```

No password is required.

---

## Normal User

A normal user cannot freely become another user.

Example:

```bash
su - john
```

Linux asks for **John's password**.

Without the correct password, switching is denied.

---

# Managing Login Shells

Every Linux user has an associated shell.

You can view it in:

```bash
cat /etc/passwd
```

Example:

```text
rakesh:x:1005:1005::/home/rakesh:/bin/bash
```

The last field shows:

```text
/bin/bash
```

This is the Login Shell.

---

# Assigning a Login Shell During User Creation

```bash
useradd -s /bin/bash rakesh
```

Now the user receives Bash as the login shell.

---

# Creating a User Without Login Access

Sometimes users should exist only for applications or services.

Such users should never log in.

Linux provides:

```text
/sbin/nologin
```

Example:

```bash
useradd -s /sbin/nologin apache
```

Even if someone knows this user's password,

Linux refuses the login.

This is commonly used for:

* Apache
* Nginx
* MySQL
* Database accounts
* Service accounts
* System applications

This improves security by preventing unnecessary interactive logins.

---

# Changing the Login Shell

Existing users can also have their shell changed.

Example:

```bash
usermod -s /bin/bash rakesh
```

or

```bash
usermod -s /sbin/nologin rakesh
```

---

# Assigning a Primary Group

While creating a user:

```bash
useradd -g developers rakesh
```

or modifying later:

```bash
usermod -g developers rakesh
```

This changes the user's **Primary Group**.

Any new files created afterward will automatically use this group.

---

# Adding Secondary Groups

Users can belong to multiple groups.

Example:

```bash
useradd -G docker,developers rakesh
```

or

```bash
usermod -G docker,developers rakesh
```

These become the user's **Secondary Groups**.

---

# The Importance of `-a` with `usermod`

One of the most common mistakes made by Linux beginners is forgetting the **append (`-a`)** option.

Suppose the user already belongs to:

```text
docker
developers
git
```

Now you execute:

```bash
usermod -G wheel rakesh
```

Result:

The existing secondary groups are removed.

Only:

```text
wheel
```

remains.

Linux **replaces** the entire secondary group list.

---

## Correct Method

Always append the new group:

```bash
usermod -aG wheel rakesh
```

Here:

* `-a` → Append
* `-G` → Secondary Groups

Now the user keeps all previous secondary groups and is simply added to the new one.

This is considered the safest and most commonly used approach in Linux administration.

---

# Adding User Comments

Sometimes administrators want to store descriptive information about a user.

Example:

```bash
useradd -c "DevOps Engineer" rakesh
```

or

```bash
usermod -c "Production Support Team" rakesh
```

The comment is stored in `/etc/passwd`.

Example:

```text
rakesh:x:1005:1005:DevOps Engineer:/home/rakesh:/bin/bash
```

This field can contain:

* Full Name
* Department
* Designation
* Team Name
* Employee Description

It is purely informational and does not affect authentication.

---

# User Home Directory

Every normal user receives a personal directory.

Example:

```text
/home/rakesh
```

This directory stores:

* Documents
* Configuration files
* Scripts
* Downloads
* Hidden configuration files (`.bashrc`, `.profile`, etc.)

Each user normally works inside their own home directory.

---

# Mail Spool Directory

Linux automatically creates a mail file for each local user.

Location:

```text
/var/spool/mail/username
```

Example:

```text
/var/spool/mail/rakesh
```

This file stores local system-generated emails, such as notifications sent by cron jobs, system utilities, or administrative tasks. While modern environments typically use external email services, the local mail spool is still available and can be useful for server administration and troubleshooting.

---

# Handling Stuck Processes

Sometimes a command may stop responding or continuously run without returning the shell prompt.

Example:

```bash
yes
```

The command continuously prints:

```text
y
y
y
y
```

To stop the running process:

```text
Ctrl + C
```

This sends an interrupt signal to the running process, terminates it, and returns control to the terminal.

This keyboard shortcut is one of the most frequently used commands while working on Linux systems.

---

# Key Takeaways

* `/etc/group` stores information about Linux groups using four colon-separated fields.
* The fourth field lists only **secondary group members**; primary users are not displayed there.
* Use the `id` command or `/etc/passwd` to verify a user's primary group.
* Every user has exactly one primary group but can belong to multiple secondary groups.
* `su username` switches the user without loading a full login environment, whereas `su - username` starts a complete login session and is the recommended method.
* Root can switch to any user without a password, while normal users must provide the target user's password.
* The `-s` option in `useradd` and `usermod` assigns a login shell such as `/bin/bash` or a non-login shell like `/sbin/nologin`.
* Use `-g` to set the primary group and `-G` to assign secondary groups.
* When adding secondary groups to an existing user, always use `usermod -aG` to append new groups without removing existing ones.
* The `-c` option stores descriptive comments about a user in `/etc/passwd`.
* User data is stored in `/home/<username>`, while local system mail is stored in `/var/spool/mail/<username>`.
* Press **Ctrl + C** to terminate a command that is stuck or running continuously.
