# Linux Package Management – Part 2 (RPM, YUM & DNF)

## Introduction

In the previous session, we learned the basics of software installation and package management in Linux.

In this session, we will understand:

- What RPM is
- Why YUM was introduced
- Why DNF replaced YUM
- How repositories work
- How Linux automatically installs dependencies
- Important package management commands used in real environments

By the end of this session, you will understand how software is managed in Red Hat-based Linux distributions such as:

- RHEL
- CentOS
- Rocky Linux
- AlmaLinux
- Oracle Linux

---

# What is a Package Manager?

A **Package Manager** is a software tool that installs, updates, removes, and manages applications on Linux.

Instead of downloading software manually and copying files into different folders, Linux uses package managers to do everything automatically.

A package manager can:

- Install software
- Remove software
- Upgrade software
- Search software
- Verify installed packages
- Handle dependencies
- Download packages from repositories

---

# Evolution of Package Managers

Linux package management has improved over time.

```
RPM
   ↓
YUM
   ↓
DNF
```

Each newer package manager solved the problems of the previous one.

---

# RPM (Red Hat Package Manager)

RPM stands for

> **Red Hat Package Manager**

It is the oldest package manager used in Red Hat-based Linux systems.

RPM works directly with **.rpm package files**.

Example:

```
httpd-2.4.57.rpm
```

---

# What Can RPM Do?

RPM can:

- Install packages
- Remove packages
- Verify packages
- Query package information

It provides only the basic package management functionality.

---

# Problems with RPM

Although RPM works, it has several major limitations.

---

## 1. Manual Package Path Required

RPM cannot search packages automatically.

It always needs the complete path.

Example

```
rpm -ivh /root/packages/httpd.rpm
```

If the file is somewhere else,

RPM cannot locate it automatically.

---

## 2. No Internet Access

RPM only installs packages that already exist on your system.

It cannot download software from the Internet.

Suppose you run

```
rpm -ivh httpd.rpm
```

If httpd requires another package,

RPM simply reports an error.

It never downloads missing packages.

---

## 3. No Dependency Resolution

This is the biggest limitation.

Imagine installing Apache.

Apache needs:

- httpd-core
- apr
- apr-util
- pcre
- openssl
- systemd libraries

RPM checks dependencies first.

If even one dependency is missing,

Installation fails.

Example:

```
error:
Failed dependencies:
httpd-core is needed
apr is needed
apr-util is needed
```

Now the administrator has to

- find every missing package
- install them one by one
- follow the correct order

This becomes extremely difficult.

---

## Example

Suppose

Package A requires Package B

Package B requires Package C

Package C requires Package D

RPM forces you to install

```
D
↓

C
↓

B
↓

A
```

Manually.

This wastes a lot of time.

---

## 4. Slow Administration

Because every dependency is installed manually,

software installation becomes slow and error-prone.

Large enterprise servers may have hundreds of dependencies.

Managing them manually is not practical.

---

# Why Was YUM Introduced?

To solve RPM's limitations,

Red Hat introduced

> **YUM**

YUM stands for

**Yellowdog Updater Modified**

It became popular around **2003**.

YUM still uses RPM packages internally,

but it adds intelligence on top of RPM.

Think of it like this:

```
RPM
↓

YUM

↓

User
```

YUM internally calls RPM,

but performs many extra tasks automatically.

---

# Advantages of YUM

---

## 1. Automatic Dependency Resolution

This is the biggest advantage.

Example

```
yum install httpd
```

YUM automatically checks

- What Apache needs
- Which packages are missing
- Which libraries are required

Then downloads everything automatically.

The user installs only one package.

YUM installs all remaining dependencies.

---

## 2. Internet Repository Support

YUM can connect to online repositories.

Example

```
yum install docker
```

YUM contacts the repository,

downloads packages,

downloads dependencies,

and installs everything automatically.

No manual downloading is required.

---

## 3. Local Repository Support

YUM does not require the Internet.

It can also work with

- DVD
- ISO
- Local Server
- Shared Folder

Large companies often create their own local repositories.

Advantages:

- Faster installation
- No Internet dependency
- Controlled software versions
- Better security

---

# What is a Repository?

A Repository is a storage location where Linux packages are kept.

Think of it as an application store for Linux.

Instead of downloading packages one by one,

Linux downloads them from repositories.

Repositories contain

- Packages
- Metadata
- Dependency information
- Package versions

---

## Repository Types

### Local Repository

Packages stored on

- DVD
- ISO
- Local Server

Example

```
file:///mnt/BaseOS
```

---

### Remote Repository

Packages stored on Internet servers.

Example

```
https://mirror.example.com
```

---

# Repository Configuration

Repository configuration files are stored inside

```
/etc/yum.repos.d/
```

Every repository configuration file ends with

```
.repo
```

Example

```
base.repo

appstream.repo

local.repo
```

---

# Default Repositories in RHEL

Two repositories are available after mounting the RHEL installation media.

---

## BaseOS

Contains

- Linux Kernel
- Core libraries
- Boot packages
- Essential operating system packages

Without BaseOS,

Linux cannot function properly.

---

## AppStream

Contains

- User applications
- Development tools
- Programming languages
- Databases
- Containers
- Extra software

Examples

- Docker
- Podman
- Python
- PHP
- Java
- NodeJS

---

# Structure of a Repository File

Example

```ini
[appstream]

name=AppStream Repository

baseurl=file:///mnt/AppStream

enabled=1

gpgcheck=0
```

---

## Explanation

### [appstream]

Unique repository ID.

Every repository needs one.

---

### name

Human-readable name.

Example

```
name=AppStream Repository
```

Shown in repository listings.

---

### baseurl

Location of packages.

Examples

Local

```
file:///mnt/BaseOS
```

Remote

```
http://server/repository
```

or

```
https://server/repository
```

---

### enabled

Controls whether the repository is active.

```
enabled=1
```

Repository is active.

```
enabled=0
```

Repository is disabled.

---

### gpgcheck

Controls package verification.

```
gpgcheck=1
```

Verify package signatures.

Recommended for production.

```
gpgcheck=0
```

Skip verification.

Useful for labs and testing.

---

# What is GPG Check?

GPG stands for

**GNU Privacy Guard**

Every official package contains a digital signature.

Before installing,

Linux checks

- Was this package modified?
- Is it genuine?
- Is it trusted?

If verification fails,

installation stops.

This protects systems from tampered software.

---

# What is repodata?

Every repository contains a folder called

```
repodata
```

This folder stores metadata.

It acts like an index.

Instead of scanning every package,

YUM reads the metadata first.

Metadata includes

- Package names
- Versions
- Dependencies
- Checksums
- Repository information

Without **repodata**, YUM or DNF cannot efficiently locate packages.

---

# DNF (Dandified YUM)

DNF is the modern replacement for YUM.

DNF stands for

> **Dandified YUM**

Almost every YUM command also works with DNF.

DNF is now the default package manager in newer Red Hat-based distributions.

---

# Why DNF Was Introduced

YUM worked well,

but had some limitations:

- Slower dependency resolution
- Higher memory usage
- Less efficient dependency solver
- Limited history management

DNF solved these issues.

---

# Advantages of DNF

## Faster Performance

Dependency calculations are much faster.

---

## Better Memory Usage

Consumes fewer system resources.

---

## Improved Dependency Solver

Handles complex dependency chains more accurately.

---

## Better Transaction History

Every operation is recorded.

Example

```
Install

Remove

Update

Downgrade
```

Everything receives a transaction ID.

---

# DNF Rollback Feature

One of DNF's most useful features is rollback.

Suppose someone accidentally removes Apache.

```
dnf remove httpd
```

Later you realize the mistake.

Instead of reinstalling manually,

check history.

```
dnf history
```

Example

```
ID   Command

10   install httpd

11   remove httpd
```

Rollback transaction 11.

```
dnf history rollback 11
```

DNF automatically restores the previous state by reversing that transaction.

This feature is extremely useful for administrators during production maintenance.

---

# YUM vs DNF

| Feature | YUM | DNF |
|----------|-----|-----|
| Uses RPM | ✅ | ✅ |
| Dependency Resolution | ✅ | ✅ |
| Internet Support | ✅ | ✅ |
| Local Repository | ✅ | ✅ |
| Faster Dependency Solver | ❌ | ✅ |
| Better Memory Usage | ❌ | ✅ |
| Better History | Limited | Advanced |
| Rollback | Limited | Yes |
| Default in Modern RHEL | No | Yes |

---

# Common YUM Commands

## List repositories

```bash
yum repolist
```

---

## Show repository information

```bash
yum repoinfo
```

---

## Install a package

```bash
yum install httpd
```

---

## Install without confirmation

```bash
yum install -y httpd
```

---

## Remove a package

```bash
yum remove httpd
```

---

## Update packages

```bash
yum update
```

---

## View package details

```bash
yum info httpd
```

---

## Find which package provides a command or file

```bash
yum whatprovides httpd
```

---

## View transaction history

```bash
yum history
```

---

## List package groups

```bash
yum group list
```

---

## Clean cached data

```bash
yum clean all
```

---

# Common DNF Commands

The syntax is almost identical.

List repositories

```bash
dnf repolist
```

---

Install package

```bash
dnf install httpd
```

---

Remove package

```bash
dnf remove httpd
```

---

Upgrade system

```bash
dnf upgrade
```

---

Package information

```bash
dnf info httpd
```

---

History

```bash
dnf history
```

---

Rollback

```bash
dnf history rollback <Transaction_ID>
```

Example

```bash
dnf history rollback 15
```

---

Package groups

```bash
dnf group list
```

---

Find package provider

```bash
dnf whatprovides <file-or-command>
```

Example

```bash
dnf whatprovides /usr/bin/python3
```

---

Clean cache

```bash
dnf clean all
```

---

# What are Package Groups?

Package Groups are collections of related software that can be installed together.

Instead of installing each package individually,

Linux installs an entire group with a single command.

Examples:

- Development Tools
- Security Tools
- Container Management
- Server with GUI
- Virtualization Host

This saves time and ensures that all required packages for a particular role are installed together.

---

# Important Definitions

## Package

A compressed software file containing binaries, configuration files, documentation, and metadata.

---

## Dependency

A library or another package that an application needs to function properly.

---

## Repository

A storage location where Linux packages and their metadata are kept.

---

## repodata

The metadata directory inside a repository that helps YUM and DNF quickly locate packages and resolve dependencies.

---

## GPG Check

A security mechanism that verifies a package's digital signature before installation.

---

## History ID

A unique transaction number assigned to every install, remove, update, or downgrade operation.

---

# Real-World Workflow

When an administrator runs:

```bash
dnf install httpd
```

The following steps occur:

1. DNF reads the enabled repository configuration files.
2. It connects to the configured repository.
3. It reads the `repodata` metadata.
4. It searches for the requested package.
5. It checks all required dependencies.
6. Missing dependencies are downloaded automatically.
7. RPM installs the packages in the correct order.
8. DNF records the entire transaction in its history database.
9. If needed later, the transaction can be reviewed or rolled back.

---

# Important Revision Questions

### Q1. What is the difference between RPM and YUM?

**Answer:** RPM is a low-level package manager that installs local RPM files but does not resolve dependencies automatically. YUM uses RPM internally and adds automatic dependency resolution along with support for local and remote repositories.

---

### Q2. Why was DNF introduced?

**Answer:** DNF was introduced to replace YUM with faster dependency resolution, better memory management, improved transaction history, and reliable rollback capabilities.

---

### Q3. Where are YUM repository configuration files stored?

**Answer:**

```text
/etc/yum.repos.d/
```

---

### Q4. What is the purpose of the `repodata` directory?

**Answer:** It contains repository metadata such as package information, versions, checksums, and dependency details, allowing YUM and DNF to search packages efficiently.

---

### Q5. What is the purpose of `gpgcheck=1`?

**Answer:** It enables digital signature verification to ensure that packages are authentic and have not been tampered with before installation.

---

# Session Summary

In this session, we learned:

- The role of Linux package managers.
- The evolution from RPM to YUM and finally to DNF.
- The limitations of RPM, especially manual dependency handling.
- How YUM introduced automatic dependency resolution and repository support.
- How repositories are configured using `.repo` files and the importance of `BaseOS`, `AppStream`, `baseurl`, `enabled`, and `gpgcheck`.
- The purpose of the `repodata` directory and GPG signature verification.
- The advantages of DNF, including faster performance, better dependency management, transaction history, and rollback.
- Frequently used YUM and DNF commands for package installation, updates, removal, repository management, history, and package groups.
- A complete package installation workflow from repository lookup to RPM installation and transaction recording.

This knowledge forms the foundation for advanced Linux administration tasks, including offline repository creation, enterprise package management, software maintenance, and troubleshooting package-related issues in production environments.
