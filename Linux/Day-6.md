
---

# Linux Training – Day 6

# User Administration in Linux
<img width="1068" height="193" alt="image" src="https://github.com/user-attachments/assets/5540282d-25d3-4d75-9808-ace363adba27" />

In the sixth session of the DevOps batch, the focus shifts from Linux file management to **User Administration**. Every Linux system is a multi-user operating system, which means multiple users can work on the same server at the same time. To keep the system secure and organized, Linux provides a complete user management mechanism.

This session introduces the security concepts behind user management, different types of Linux users, groups, user creation commands, and the important system files that are modified whenever a user account is created.

---

# Why User Administration is Important

In a production environment, many people and applications use the same Linux server.

For example:

* System administrators manage the server.
* Developers deploy applications.
* Database administrators manage databases.
* Applications themselves run under dedicated service accounts.

Linux must ensure that every user has only the permissions they need. This is achieved through user accounts, groups, and permissions.

---

# The AAA Concept

The **AAA model**, is the foundation of Linux security.

AAA stands for:

* Authentication
* Authorization
* Audit

---

## 1. Authentication

Authentication means **verifying the identity of a user**.

Before allowing access to the system, Linux first checks whether the user is really who they claim to be.

Common authentication methods include:

* Username and password
* SSH key authentication
* Smart cards
* Biometric authentication
* Multi-Factor Authentication (MFA)

Example:

A user enters:

```
Username: rakesh
Password: ********
```

Linux verifies the credentials before allowing login.

---

## 2. Authorization

After authentication is successful, Linux decides **what the user is allowed to do**.

Authorization determines permissions such as:

* Which files can be read
* Which directories can be accessed
* Which commands can be executed
* Which services can be managed

Example:

A developer may have permission to deploy applications but not reboot the server.

---

## 3. Audit

Audit means **recording every important activity performed by users**.

Linux keeps logs of actions such as:

* User login
* User logout
* File creation
* File deletion
* Software installation
* Command execution
* Service restart

These logs help administrators:

* Track user activity
* Investigate security incidents
* Troubleshoot problems
* Meet compliance requirements

---

# Types of Linux Users

Every user in Linux is identified internally using a **User ID (UID)**.

Although users log in using usernames, Linux actually recognizes them by their UID.

There are three main categories of users.

---

## 1. Root User (Super User)

The Root user is the administrator of the entire Linux system.

Characteristics:

* Has complete control over the operating system
* Can access every file and directory
* Can install or remove software
* Can create and delete users
* Can change system configurations
* Can modify permissions

Important:

```
UID = 0
```

Any account with **UID 0** is treated as the Root user.

---

## 2. System Users (Application Users)

System users are created automatically during the installation of Linux or software packages.

These users are **not meant for humans**.

Instead, applications and background services use them.

Examples include:

* mail
* shutdown
* nginx
* apache
* mysql
* postgres

Characteristics:

* Usually cannot log in
* Used only by services
* Improve security by isolating applications

Typical UID range:

```
1 – 999
```

Their login shell is usually:

```
/sbin/nologin
```

or

```
/usr/sbin/nologin
```

which prevents interactive login.

---

## 3. Local Users (Regular Users)

These are the accounts created for real people.

Examples:

* Developers
* DevOps Engineers
* Testers
* Students
* Database Administrators

Characteristics:

* Can log in normally
* Have a personal home directory
* Perform daily work
* Have limited permissions

Default UID range:

```
1000 – 60000
```

---

# Understanding Groups

Managing permissions for individual users becomes difficult as the number of users increases.

Linux solves this problem using **Groups**.

Instead of assigning permissions to every user individually, permissions can be assigned to groups.

---

## Primary Group

Every user **must have exactly one Primary Group**.

If no group is specified while creating a user, Linux automatically creates a group with the same name as the user.

Example:

```
User:
rakesh

Primary Group:
rakesh
```

---

## Secondary (Supplementary) Groups

A user can belong to multiple additional groups.

These groups provide extra permissions.

Example:

User:

```
rakesh
```

Secondary Groups:

```
docker
wheel
developers
git
```

This allows the user to access Docker, administrative commands, and shared project resources without changing the primary group.

---

# Essential User Management Commands

## Create a User

```
useradd username
```

Example:

```bash
useradd rakesh
```

Creates a new user account.

---

## Display User Information

```
id username
```

Example:

```bash
id rakesh
```

Sample Output:

```bash
uid=1001(rakesh)
gid=1001(rakesh)
groups=1001(rakesh)
```

The command displays:

* UID
* Primary Group ID (GID)
* All associated groups

---

## Create a Group

```
groupadd groupname
```

Example:

```bash
groupadd developers
```

---

## Delete a User

```
userdel username
```

Example:

```bash
userdel rakesh
```

This removes the user account only.

By default, Linux does **not** remove:

* Home directory
* Mail files

---

## Delete User with Home Directory

```
userdel -r username
```

Example:

```bash
userdel -r rakesh
```

The `-r` option removes:

* User account
* Home directory
* Mail spool
* User-owned files within the home directory

---

## Create User with Custom UID

```
useradd -u UID username
```

Example:

```bash
useradd -u 2001 devuser
```

This creates the user with a manually assigned UID.

---

# Seven Important System Files Modified During User Creation

Whenever a new user is created, Linux updates several important files automatically.

Understanding these files is essential for Linux administration.

---

## 1. `/etc/passwd`

Stores basic information about every user account.

Contains:

* Username
* UID
* GID
* Home directory
* Login shell

---

## 2. `/etc/group`

Stores group information.

Contains:

* Group name
* Group ID (GID)
* Group members

---

## 3. `/etc/shadow`

Stores encrypted password information.

Contains:

* Encrypted password
* Password expiry
* Password aging details

Only the Root user can read this file.

---

## 4. `/etc/gshadow`

Stores secure group password information.

Contains:

* Encrypted group passwords
* Group administrators
* Group members

---

## 5. `/home`

This directory contains the personal home directories of all regular users.

Example:

```
/home/rakesh
/home/rahul
/home/anita
```

---

## 6. `/var/spool/mail`

Stores mailbox files for each user.

Example:

```
/var/spool/mail/rakesh
```

---

## 7. `/etc/skel`

This directory contains the default files that are copied into every new user's home directory.

Typical files include:

```
.bashrc
.bash_profile
.bash_logout
```

When a new user is created, Linux copies these files automatically into the user's home directory.

---

# Understanding the Structure of `/etc/passwd`

Every line in `/etc/passwd` represents one user account.

The fields are separated by colons (`:`).

Structure:

```
Username : Password Placeholder : UID : GID : Comment : Home Directory : Login Shell
```

Example:

```text
rakesh:x:1001:1001:Rakesh Kumar:/home/rakesh:/bin/bash
```

Explanation:

| Field                | Description                                                               |
| -------------------- | ------------------------------------------------------------------------- |
| Username             | Login name of the user                                                    |
| Password Placeholder | Usually `x`, indicating the encrypted password is stored in `/etc/shadow` |
| UID                  | Unique User ID                                                            |
| GID                  | Primary Group ID                                                          |
| Comment              | User description (GECOS field)                                            |
| Home Directory       | User's personal working directory                                         |
| Login Shell          | Default shell executed after login                                        |

---

# Login Shells

The last field of `/etc/passwd` specifies the user's default shell.

Examples:

Interactive login shell:

```text
/bin/bash
```

This allows users to log in and execute commands.

Non-login shell:

```text
/sbin/nologin
```

or

```text
/usr/sbin/nologin
```

This prevents interactive logins and is commonly assigned to system users that run background services.

---

# Summary

In this session, we learned:

* The **AAA model**: Authentication, Authorization, and Audit.
* The three types of Linux users: Root, System, and Local users.
* The importance of **UIDs** and **Groups**.
* The difference between **Primary** and **Secondary Groups**.
* Essential user management commands such as `useradd`, `userdel`, `groupadd`, and `id`.
* The seven critical system files updated during user creation.
* The structure and purpose of the `/etc/passwd` file and how Linux identifies users internally.
