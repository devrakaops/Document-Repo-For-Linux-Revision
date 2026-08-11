
## Linux Package Management and Repository Configuration — Part-4

### 1. Why do we need YUM/DNF when RPM already exists?

In the previous session, we learned about **RPM (Red Hat Package Manager)**.

RPM can install an `.rpm` package, but it has an important limitation: **RPM works at the individual package level and does not automatically solve dependencies for us.**

For example, suppose we want to install Apache:

```bash
rpm -ivh httpd.rpm
```

The `httpd` package may require other packages such as `httpd-core` and additional libraries.

If those dependencies are missing, RPM reports an error.

The administrator then has to:

1. Find the missing dependency.
2. Download or locate its RPM package.
3. Install that dependency.
4. Check whether another dependency is missing.
5. Repeat the process.

This is commonly called **dependency hell**.

Imagine an application requiring 20 or 50 additional packages. Managing all of those packages manually becomes difficult.

RPM also does not provide the same repository-based package discovery and automatic internet/repository download mechanism that YUM/DNF provides.

Therefore, a higher-level package management tool was needed.

---

# 2. YUM — Yellowdog Updater, Modified

**YUM** stands for:

> **Yellowdog Updater, Modified**

YUM was developed to make RPM package management easier.

The important point is:

```text
Administrator
      |
      v
     YUM
      |
      v
     RPM
      |
      v
RPM Packages
```

YUM works as a **high-level package manager** while RPM remains the underlying package format/package management technology.

Instead of manually installing every dependency, we can simply run:

```bash
yum install httpd
```

YUM checks the configured repositories, finds `httpd`, checks its dependencies, downloads the required packages and installs them in the correct order.

This is the major advantage of repository-based package management.

---

# 3. What is a Repository?

A **repository** is a location where Linux software packages are stored along with the metadata required to manage those packages.

A repository may be available through:

```text
Local Disk
ISO/DVD
HTTP
HTTPS
Network Server
```

For example:

```text
Repository
│
├── package1.rpm
├── package2.rpm
├── package3.rpm
├── package4.rpm
└── repodata/
```

The `.rpm` files are the actual software packages.

The `repodata` directory contains metadata about those packages.

---

# 4. BaseOS and AppStream

In Red Hat Enterprise Linux environments, installation media commonly provides two major repositories:

```text
BaseOS
AppStream
```

## BaseOS

**BaseOS** contains the fundamental packages required for the operating system.

Examples include:

* Kernel-related packages
* Core system utilities
* Essential libraries
* Basic system components

Think of BaseOS as:

> **Packages required to build and operate the basic operating system.**

---

## AppStream

**AppStream** contains additional applications and user-space software.

Examples include:

* Development tools
* Programming environments
* Database software
* Web servers
* Additional applications

Think of AppStream as:

> **Additional applications and software that run on top of the core operating system.**

So, conceptually:

```text
RHEL System
│
├── BaseOS
│     └── Core operating system
│
└── AppStream
      └── Additional applications
```

---

# 5. What is `repodata`?

This is one of the most important concepts in repository configuration.

Inside a repository, we normally find:

```text
repodata/
```

`repodata` contains repository metadata.

You can think of it like the **index of a book**.

Suppose a repository contains thousands of RPM packages.

YUM/DNF should not blindly inspect every RPM file whenever you ask:

```bash
dnf install httpd
```

Instead, it uses the repository metadata to understand:

* Which packages are available
* Package versions
* Package architectures
* Package descriptions
* Dependencies
* Package relationships

Therefore:

```text
Repository
│
├── RPM Packages
│
└── repodata
       │
       ├── Package metadata
       ├── Dependency information
       ├── Version information
       └── Repository information
```

Without valid repository metadata, YUM/DNF cannot properly use that directory as a normal repository.

---

# 6. Repository Configuration Files

YUM/DNF repository configuration files are normally stored under:

```bash
/etc/yum.repos.d/
```

Check the directory:

```bash
ls /etc/yum.repos.d/
```

You may see files such as:

```text
redhat.repo
appstream.repo
baseos.repo
```

A repository configuration file must normally use the:

```text
.repo
```

extension.

For example:

```text
myrepo.repo
```

---

# 7. Structure of a `.repo` File

A basic repository configuration looks like this:

```ini
[myrepo]
name=My Local Repository
baseurl=file:///path/to/repository
enabled=1
gpgcheck=0
```

Let's understand every parameter.

### `[myrepo]`

This is the **Repository ID**.

Example:

```ini
[baseos]
```

The ID should be unique within the repository configuration.

---

### `name`

This is the human-readable name of the repository.

Example:

```ini
name=My BaseOS Repository
```

---

### `baseurl`

This tells YUM/DNF **where the repository is located**.

For a local filesystem repository:

```ini
baseurl=file:///path/to/repository
```

For an HTTP repository:

```ini
baseurl=http://server.example.com/repo
```

For HTTPS:

```ini
baseurl=https://server.example.com/repo
```

The protocol is important because it tells YUM/DNF how to access the repository.

---

### `enabled`

This controls whether the repository is active.

Enabled:

```ini
enabled=1
```

Disabled:

```ini
enabled=0
```

---

### `gpgcheck`

This controls package signature verification.

Enabled:

```ini
gpgcheck=1
```

Disabled:

```ini
gpgcheck=0
```

In production environments, package signature verification should be handled properly rather than simply disabling security checks.

---

# 8. Checking Configured Repositories

To see enabled repositories:

```bash
yum repolist
```

On modern RHEL systems, the equivalent command is:

```bash
dnf repolist
```

Example:

```text
repo id       repo name
baseos        BaseOS
appstream     AppStream
```

This helps us confirm whether our repository configuration is working.

---

# 9. Repository Information

To see more detailed repository information:

```bash
yum repoinfo
```

or:

```bash
dnf repoinfo
```

This can provide information such as:

* Repository ID
* Repository name
* Repository URL
* Package count
* Repository size
* Enabled/disabled state
* Metadata information

---

# 10. Installing Packages

The most common operation is installing a package.

```bash
yum install httpd
```

or:

```bash
dnf install httpd
```

If we don't want an interactive confirmation:

```bash
yum install httpd -y
```

or:

```bash
dnf install httpd -y
```

The important advantage is that YUM/DNF automatically handles dependencies.

Conceptually:

```text
dnf install httpd
        |
        v
Find httpd
        |
        v
Check dependencies
        |
        v
Find required packages
        |
        v
Download/install dependencies
        |
        v
Install httpd
```

---

# 11. Removing Packages

To remove a package:

```bash
yum remove httpd
```

or:

```bash
dnf remove httpd
```

For automatic confirmation:

```bash
dnf remove httpd -y
```

Before removing packages from a production system, always check what else will be removed.

---

# 12. Updating Packages

To update available packages:

```bash
yum update
```

or:

```bash
dnf update
```

This checks the configured repositories and updates installed packages when newer versions are available.

---

# 13. Package History

YUM/DNF maintains a transaction history.

Use:

```bash
yum history
```

or:

```bash
dnf history
```

You can see information such as:

```text
Transaction ID
Date and Time
Command
Number of packages changed
Action performed
```

For example:

```text
ID    Command
1     install httpd
2     install nginx
3     remove httpd
```

This is extremely useful for troubleshooting.

If someone says:

> "The server was working yesterday, but something changed today."

The administrator can inspect package transactions and determine what package operation occurred.

---

# 14. Searching for a Package

To check whether a package is available:

```bash
yum list httpd
```

or:

```bash
dnf list httpd
```

This can show whether the package is:

* Installed
* Available
* Available from a particular repository

For example:

```text
Available Packages
httpd.x86_64
```

---

# 15. Package Information

To get detailed information about a package:

```bash
yum info httpd
```

or:

```bash
dnf info httpd
```

It can show:

* Package name
* Version
* Release
* Architecture
* Repository
* Size
* Summary
* Description

This is useful before installing a package when you want to understand exactly what package version is available.

---

# 16. Finding Which Package Provides a File

Sometimes we know a command or file but don't know which package provides it.

For example:

```bash
yum whatprovides /usr/bin/somecommand
```

or:

```bash
dnf provides /usr/bin/somecommand
```

This answers:

> **Which package provides this file?**

This is particularly useful when a command is missing from the system.

For example, if you need a particular command but don't know which RPM contains it, `whatprovides`/`provides` can help identify the package.

---

# 17. Package Groups

Linux repositories can also contain **package groups**.

A package group is a collection of related packages.

For example:

```text
Development Tools
```

may contain many development-related packages.

To list available groups:

```bash
yum grouplist
```

Modern DNF systems commonly use:

```bash
dnf group list
```

To install a group:

```bash
yum groupinstall "RPM Development Tools"
```

or:

```bash
dnf group install "RPM Development Tools"
```

Instead of installing every package individually, we can install the complete group.

---

# 18. Cleaning Package Cache

YUM/DNF maintains cache and metadata locally.

Over time, cached data can consume disk space.

To clean the cache:

```bash
yum clean all
```

or:

```bash
dnf clean all
```

This removes cached repository metadata and downloaded package cache.

It **does not remove installed applications**.

This distinction is important:

```text
dnf clean all
       |
       └── Cleans cache
           
Installed packages
       |
       └── Remain installed
```

---

# 19. DNF — Dandified YUM

Modern Red Hat systems use **DNF** as the primary package manager.

DNF means:

> **Dandified YUM**

The important relationship is:

```text
Old systems
    |
   YUM
    |
   RPM

Modern systems
    |
   DNF
    |
   RPM
```

On modern RHEL systems, the `yum` command is largely retained for compatibility and maps into the DNF ecosystem.

Therefore, many commands look almost identical:

```bash
yum install httpd
dnf install httpd
```

```bash
yum remove httpd
dnf remove httpd
```

```bash
yum update
dnf update
```

```bash
yum history
dnf history
```

The exact behavior and available subcommands can differ by RHEL/version, so administrators should use the documentation for the specific OS release.

---

# 20. DNF History and Rollback

DNF maintains package transaction history.

First check:

```bash
dnf history
```

Suppose we see:

```text
ID
10
11
12
13
```

We can inspect a particular transaction:

```bash
dnf history info 13
```

This helps us understand what happened during transaction 13.

DNF also provides transaction undo/rollback functionality.

For example:

```bash
dnf history undo 13
```

means:

> Attempt to undo the changes made by transaction 13.

There is also:

```bash
dnf history rollback <ID>
```

which attempts to roll the package transaction history back to the specified point.

### Important distinction

Do **not** explain this to students as:

> "Rollback is the same as restoring a complete server snapshot."

It is not.

DNF works at the **package transaction level**. It does not restore arbitrary configuration files, application data, database contents, or the complete filesystem state like a VM snapshot or backup would.

For production recovery:

```text
DNF rollback
     ≠
Complete system backup/restore
```

---

# 21. Complete Command Revision

| Task                 | YUM                    | DNF                         |
| -------------------- | ---------------------- | --------------------------- |
| List repositories    | `yum repolist`         | `dnf repolist`              |
| Repository details   | `yum repoinfo`         | `dnf repoinfo`              |
| Install              | `yum install httpd`    | `dnf install httpd`         |
| Remove               | `yum remove httpd`     | `dnf remove httpd`          |
| Update               | `yum update`           | `dnf update`                |
| History              | `yum history`          | `dnf history`               |
| Package list         | `yum list httpd`       | `dnf list httpd`            |
| Package information  | `yum info httpd`       | `dnf info httpd`            |
| Find provider        | `yum whatprovides ...` | `dnf provides ...`          |
| Package groups       | `yum groupinstall ...` | `dnf group install ...`     |
| Clean cache          | `yum clean all`        | `dnf clean all`             |
| Transaction undo     | —                      | `dnf history undo <ID>`     |
| Transaction rollback | —                      | `dnf history rollback <ID>` |

---

# 22. The Complete Flow to Remember

The entire Day 14 session can be remembered using this flow:

```text
RPM
 |
 | Dependency problems
 | No repository intelligence
 v
YUM
 |
 | Repository-based management
 | Automatic dependency resolution
 v
Repository
 |
 +---- BaseOS
 |
 +---- AppStream
 |
 +---- repodata
 |
 v
.repo configuration
 |
 v
/etc/yum.repos.d/
 |
 v
YUM / DNF
 |
 +---- install
 +---- remove
 +---- update
 +---- list
 +---- info
 +---- history
 +---- provides
 +---- group
 +---- clean
 |
 v
DNF
 |
 +---- Better dependency handling
 +---- Modern package management
 +---- Transaction history
 +---- Undo / rollback capabilities
```

### Final exam/practical understanding

If you remember only the core logic of this session, remember these five points:

1. **RPM installs individual `.rpm` packages but does not provide the same automatic dependency/repository management as YUM/DNF.**
2. **YUM introduced repository-based and automatic dependency management around RPM.**
3. **A repository contains packages plus `repodata`, which provides the metadata needed by the package manager.**
4. **Repository configuration files are stored in `/etc/yum.repos.d/` and use the `.repo` extension.**
5. **DNF is the modern package manager in RHEL-based systems, with transaction history and package-level undo/rollback capabilities.**
