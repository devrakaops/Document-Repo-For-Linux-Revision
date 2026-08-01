# Day-7: Linux Permission Management – Part 1 (Ownership, Recursive Permissions & UMASK)


Now we move to another important area:

* Changing file ownership
* Changing group ownership
* Applying changes recursively
* Understanding the role of Execute permission on directories
* Learning UMASK (Default Permission Policy)

These concepts are heavily used by Linux Administrators, DevOps Engineers, Kubernetes Administrators, and System Administrators because almost every application creates its own user account and requires proper ownership of files.

---

# Why Do We Need to Change Ownership?

Suppose you install an application like:

* Apache
* Nginx
* Jenkins
* Tomcat
* MySQL
* PostgreSQL

Most applications create their own Linux user.

For example:

```
apache
nginx
mysql
jenkins
tomcat
```

These users are responsible for reading and writing their application's files.

Imagine this situation:

```
/opt/myapp/
```

Currently owned by:

```
root
```

But the application runs using:

```
jenkins
```

Now Jenkins cannot modify its own files because it is not the owner.

So we change ownership.

---

# Changing File Owner (chown)

The command used is:

```bash
chown
```

Syntax:

```bash
chown username filename
```

Example

Current owner:

```
root
```

Change owner to:

```
jenkins
```

Command:

```bash
chown jenkins test.txt
```

Verify:

```bash
ls -l test.txt
```

Output

```
-rw-r--r-- 1 jenkins root ...
```

Now the owner has become:

```
jenkins
```

---

# Changing Group Ownership (chgrp)

Sometimes we don't want to change the owner.

We only want to change the group.

For this Linux provides:

```bash
chgrp
```

Syntax

```bash
chgrp groupname filename
```

Example

```bash
chgrp developers test.txt
```

Verify

```bash
ls -l test.txt
```

Output

```
-rw-r--r-- 1 jenkins developers
```

Only the group has changed.

---

# Changing Owner and Group Together

Instead of running two commands:

```bash
chown
```

and

```bash
chgrp
```

Linux allows both in one command.

Syntax

```bash
chown owner:group file
```

Example

```bash
chown apache:webserver index.html
```

Now

Owner

```
apache
```

Group

```
webserver
```

Both changed together.

---

# Change Only Group Using chown

Many people don't know this trick.

Suppose you only want to change the group using `chown`.

Syntax

```bash
chown :group filename
```

Notice

```
:
```

Nothing before the colon.

Example

```bash
chown :developers test.txt
```

Meaning

```
Owner → No Change

Group → developers
```

This produces the same result as

```bash
chgrp developers test.txt
```

---

# Understanding Recursive Operations

Assume we have a directory.

```
project/
│
├── app.py
├── config.yml
├── logs/
│   └── app.log
└── scripts/
    └── deploy.sh
```

Suppose owner is

```
root
```

You execute

```bash
chown jenkins project
```

What changes?

Only

```
project
```

The files inside remain owned by:

```
root
```

This surprises many beginners.

---

# Recursive Option (-R)

To change everything inside a directory, use:

```bash
-R
```

R stands for

```
Recursive
```

It means

> Apply the operation to every file and every subdirectory inside the directory.

Example

```bash
chown -R jenkins project
```

Now

```
project
project/app.py
project/config.yml
project/logs
project/logs/app.log
project/scripts
project/scripts/deploy.sh
```

Everything becomes owned by

```
jenkins
```

---

# Recursive Group Change

Example

```bash
chgrp -R developers project
```

Now every file and every folder gets

```
developers
```

as its group.

---

# Recursive Owner and Group Together

Example

```bash
chown -R apache:webserver website/
```

Everything inside

```
website/
```

gets

Owner

```
apache
```

Group

```
webserver
```

---

# Understanding Execute Permission on Directories

One of the most confusing topics in Linux is:

> What does Execute (`x`) mean for a directory?

Most beginners think:

```
Execute means run a program.
```

That is true for files.

But **directories behave differently.**

---

# Execute Permission on Files

For files

```
x
```

means

```
Run the file as a program or script.
```

Example

```
script.sh
```

Without execute permission

```
./script.sh
```

will fail.

---

# Execute Permission on Directories

For directories

```
Execute
```

means

```
You are allowed to enter or access the directory.
```

Think of it as the **door** to the directory.

Without the door, you cannot go inside.

---

# Read Permission on Directories

Read permission means

```
You can see the names of files.
```

But that doesn't mean you can access them.

---

# Write Permission on Directories

Write permission means

You can

* create files
* delete files
* rename files

But only if you are allowed to access the directory.

---

# Why Execute Permission Is Important

Imagine a room.

The room has:

* Glass window
* Door

Window

```
Read Permission
```

Door

```
Execute Permission
```

You can look through the window.

But can you enter?

No.

Without the door you cannot:

* create anything
* modify anything
* access files inside

The execute permission acts like the door.

---

# Example

Suppose permissions are

```
rw-
```

There is

```
Read

Write

No Execute
```

Result

You may know the directory exists.

But you cannot properly access files inside it.

Even operations like traversing the directory or working with its contents will fail because there is no execute permission.

This is why **Read + Write alone are not sufficient on a directory**—Execute is required to enter and use it effectively.

---

# What is UMASK?

Whenever we create

* files
* directories

Linux automatically assigns permissions.

Question

Who decides those permissions?

Answer

```
UMASK
```

UMASK stands for

```
User Mask
```

It defines which permission bits are removed from the default permissions during file or directory creation.

---

# Default Permission Values

Linux starts from maximum default values.

For directories

```
777
```

Meaning

```
rwxrwxrwx
```

For files

```
666
```

Meaning

```
rw-rw-rw-
```

Notice

Files do **not** receive execute permission by default because every file is not intended to be executable.

---

# UMASK Calculation

Formula

## Directory

```
777
-
UMASK
=
Final Permission
```

## File

```
666
-
UMASK
=
Final Permission
```

---

# Example 1

Current UMASK

```
022
```

Directory

```
777
-022
----
755
```

Permission

```
rwxr-xr-x
```

---

File

```
666
-022
----
644
```

Permission

```
rw-r--r--
```

---

# Example 2

UMASK

```
002
```

Directory

```
777
-002
----
775
```

File

```
666
-002
----
664
```

This is common in collaborative environments where users in the same group need write access.

---

# Checking Current UMASK

Command

```bash
umask
```

Output

```
0022
```

Notice

Linux usually shows four digits.

Example

```
0022
```

---

# Meaning of Four Digits

Example

```
0022
```

Breakdown

```
0 0 2 2
```

First digit

```
Special Permissions

SUID

SGID

Sticky Bit
```

The remaining three digits represent:

* User
* Group
* Others

These special permissions will be covered in the next session.

---

# Setting Temporary UMASK

Command

```bash
umask 027
```

Now create a file

```bash
touch demo.txt
```

Check permissions

```bash
ls -l demo.txt
```

The new file will follow the new UMASK.

---

# Is It Permanent?

No.

The change exists only for the current shell session.

It will be lost when:

* terminal closes
* user logs out
* system reboots

---

# Permanent UMASK for One User

Every user has

```
~/.bashrc
```

Open it

```bash
vi ~/.bashrc
```

Add

```bash
umask 027
```

Reload

```bash
source ~/.bashrc
```

Now every new terminal for that user will automatically use

```
027
```

---

# System-Wide Default UMASK for New Users

Linux also stores a default UMASK configuration in:

```bash
/etc/login.defs
```

Look for

```text
UMASK 022
```

Modify if required

```text
UMASK 027
```

This sets the default UMASK policy used for newly created user accounts and login defaults.

---

# Session Summary

In this session, we learned:

* How to change the owner of files using `chown`
* How to change the group using `chgrp`
* How to change both owner and group together
* How to use `chown :group` to change only the group
* Why recursive operations (`-R`) are required for directories
* The difference between execute permission on files and directories
* The purpose of UMASK
* Why directories start with `777` and files with `666`
* How to calculate permissions using UMASK
* How to view the current UMASK
* How to configure UMASK temporarily
* How to make UMASK permanent for a single user using `.bashrc`
* How `/etc/login.defs` provides a system-wide default UMASK policy for new users

---

# Commands Covered in This Session

```bash
# Change owner
chown user file

# Change group
chgrp group file

# Change owner and group together
chown user:group file

# Change only group using chown
chown :group file

# Recursive owner change
chown -R user directory

# Recursive group change
chgrp -R group directory

# Recursive owner and group change
chown -R user:group directory

# Check permissions
ls -l

# Display current UMASK
umask

# Set temporary UMASK
umask 022
umask 027

# Edit per-user shell configuration
vi ~/.bashrc
source ~/.bashrc

# View or edit login defaults
vi /etc/login.defs
```

> **Note:** The session ended with an assignment to discover a **fourth method** of applying a UMASK policy to **all existing users** simultaneously. The next session will introduce **Special Permissions** (SUID, SGID, Sticky Bit) and **ACL (Access Control Lists)**.
