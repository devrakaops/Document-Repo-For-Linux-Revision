---

# Day-6 (Part-3) – User and Group Administration

In this session, the instructor continues the topic of **User Administration** by explaining password management, password aging policies, group security, user environment configuration, and system-wide user defaults. These concepts are essential for every Linux System Administrator because they help control user security and account management.

---

# Understanding `/etc/shadow`

The `/etc/shadow` file is one of the most important security files in Linux.

Unlike `/etc/passwd`, which stores general user information, **`/etc/shadow` stores password-related information and password aging policies.**

For security reasons, only the **root user** can read this file.

```bash
cat /etc/shadow
```

Example:

```text
student:$6$abcxyz...:20280:0:90:7:10:20500:
```

Each line belongs to one user and contains **9 fields**, separated by colons (`:`).

---

## Structure of `/etc/shadow`

```
username:password:lastchange:min:max:warning:inactive:expire:reserved
```

---

## Field 1 – Username

This field stores the username.

Example:

```
student
```

It must match the username present in `/etc/passwd`.

---

## Field 2 – Encrypted Password

Linux never stores passwords in plain text.

Instead, passwords are stored in **encrypted (hashed)** form using secure hashing algorithms such as:

* SHA-256
* SHA-512 (most commonly used)
* Other modern hashing algorithms depending on the Linux distribution

Example:

```
$6$abcxyz......
```

Here,

```
$6$
```

indicates SHA-512 hashing.

### Special Symbols

#### `!!`

```
student:!!:
```

Meaning:

* Password has not been set.
* User cannot log in using a password.

---

#### `!`

Sometimes a password begins with `!`

Example

```
!$6$abcd....
```

This means the account has been locked.

---

#### `*`

Some system accounts contain

```
*
```

which means password login is disabled.

---

## Field 3 – Last Password Change

Example

```
20280
```

This number represents the number of days since

> January 1, 1970

This date is known as the **Unix Epoch**.

Linux stores time internally by counting from this date.

---

## Field 4 – Minimum Password Age

Example

```
0
```

This specifies the minimum number of days before a user is allowed to change the password again.

Example

```
5
```

means the user must wait five days before changing the password.

Purpose:

It prevents users from changing passwords repeatedly just to bypass password history policies.

---

## Field 5 – Maximum Password Age

Example

```
90
```

This means

The password expires after **90 days**.

After expiry, Linux forces the user to choose a new password.

This is an important security policy in enterprise environments.

---

## Field 6 – Warning Days

Example

```
7
```

The system starts displaying warning messages

```
Your password will expire in 7 days.
```

This gives users enough time to change their password before it expires.

---

## Field 7 – Inactive Days (Grace Period)

Example

```
10
```

Suppose

* Password expires today.
* Inactive period = 10 days.

The user can still log in for the next **10 days**, but Linux immediately forces a password change.

After the grace period ends, the account becomes locked until an administrator unlocks or resets it.

---

## Field 8 – Account Expiration Date

Example

```
20500
```

This also represents the number of days since January 1, 1970.

Unlike password expiry, this field defines **when the entire user account expires**.

After this date:

* Login is not allowed.
* Password changes are not possible.
* The account becomes inactive.

This is commonly used for:

* Temporary employees
* Internship accounts
* Student lab accounts
* Contract-based users

---

## Field 9 – Reserved Field

Currently unused.

It is reserved for future Linux features.

Normally this field remains empty.

---

# Viewing Password Aging Information

Instead of manually reading `/etc/shadow`, Linux provides the **`chage`** command.

---

## Display Password Aging Details

```bash
chage -l username
```

Example

```bash
chage -l student
```

Output:

```text
Last password change
Password expires
Password inactive
Account expires
Minimum days
Maximum days
Warning days
```

This is the safest way to check password policies.

---

# Configuring Password Aging

Run

```bash
chage username
```

Example

```bash
chage student
```

Linux asks interactive questions such as

```
Minimum Password Age
Maximum Password Age
Warning Days
Inactive Days
Account Expiration
```

After entering the values, Linux automatically updates `/etc/shadow`.

---

## Common `chage` Options

Display details:

```bash
chage -l student
```

Set maximum password age:

```bash
chage -M 90 student
```

Set minimum password age:

```bash
chage -m 5 student
```

Set warning days:

```bash
chage -W 7 student
```

Set inactive (grace) days:

```bash
chage -I 10 student
```

Expire the account on a specific date:

```bash
chage -E 2026-12-31 student
```

---

## Why Not Edit `/etc/shadow` Manually?

Although you can edit the file using:

```bash
vi /etc/shadow
```

or

```bash
vim /etc/shadow
```

this is **not recommended**.

Reasons:

* One typing mistake can corrupt the file.
* Incorrect formatting may prevent user logins.
* System authentication may fail.

Always prefer commands like:

* `passwd`
* `chage`
* `usermod`

These commands validate input and safely update system files.

---

# Understanding `/etc/gshadow`

Just as `/etc/shadow` stores password information for users, **`/etc/gshadow`** stores security-related information for groups.

Only the **root user** can read this file.

```bash
cat /etc/gshadow
```

---

## Structure

```
groupname:password:administrators:members
```

Each record has **4 fields**.

---

## Field 1 – Group Name

Example

```
developers
```

---

## Field 2 – Group Password

Normally empty.

A password can be assigned using:

```bash
gpasswd developers
```

Although group passwords exist, they are rarely used in modern Linux systems.

---

## Field 3 – Group Administrators

Users listed here can manage the group without being root.

They can:

* Add members
* Remove members
* Manage group membership

Example

```
alice,bob
```

---

## Field 4 – Group Members

This field contains all **secondary group members**.

Example

```
student1,student2,student3
```

---

# Skeleton Directory (`/etc/skel`)

The instructor described `/etc/skel` as the **backbone of new user creation**.

Whenever a new user is created using

```bash
useradd username
```

Linux copies everything from

```
/etc/skel
```

into the new user's home directory.

This automatically prepares the user's working environment.

---

## View Skeleton Files

```bash
ls -la /etc/skel
```

Example

```text
.bashrc
.bash_profile
.bash_logout
```

These are hidden files because they begin with a dot (`.`).

---

## Purpose of Skeleton Files

These files configure:

* Shell environment
* Command aliases
* Environment variables
* Prompt appearance
* Default shell settings
* Login behavior

Every new user automatically receives these configuration files.

---

# System-Wide User Configuration Files

Linux provides several files that define default behavior for new users.

---

## `/etc/default/useradd`

Contains default values used by the `useradd` command.

View defaults:

```bash
useradd -D
```

Typical defaults include:

* Home directory location
* Default shell
* Default group
* Inactive days
* Skeleton directory

Changing these values affects **future users only**, not existing accounts.

---

## `/etc/login.defs`

This file contains system-wide login and password configuration.

Typical settings include:

* Password aging defaults
* UID ranges
* GID ranges
* Mail directory
* Home directory creation
* Password length requirements

Many user management commands read values from this file.

---

## `/etc/security/limits.conf`

This file controls **resource limits** for users.

Examples:

* Maximum open files
* Maximum processes
* CPU time
* Memory usage
* Stack size
* Core dump size

This is widely used on production servers to prevent a single user or application from consuming all system resources.

---

# Important Lesson

* The **root user** has unrestricted access to the entire system.
* Commands like `rm -rf` should be used with extreme caution.
* Always verify the current directory (`pwd`) and the command before pressing **Enter**.
* On production systems, a single mistake by the root user can cause downtime or permanent data loss.

---

# Summary

In this session, we learned:

* The purpose of the `/etc/shadow` file and its 9 fields.
* How Linux stores encrypted passwords and manages password aging.
* How to use the `chage` command to view and configure password policies safely.
* The role of the `/etc/gshadow` file in managing group security and administrators.
* How the `/etc/skel` directory provides default configuration files for every new user.
* The importance of `/etc/default/useradd`, `/etc/login.defs`, and `/etc/security/limits.conf` in defining system-wide user defaults.
* Why editing sensitive system files manually is risky and why administrative commands should be preferred.
* A practical lesson on the power and responsibility of working as the **root** user, emphasizing careful use of commands like `rm -rf`.
