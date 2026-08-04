



# Package Management in Linux – Part 1

> **Session Goal:**  
> In this session, we learn how software is installed in Linux, what Package Managers are, how RPM packages work, and how to manage software using the `rpm` command.

---

# What is Package Management?

Whenever we want to install any software in Linux (such as Apache, Docker, Git, Nginx, Java, etc.), we need a mechanism that can:

- Install the software correctly
- Place all files in their proper locations
- Set the correct permissions
- Configure ownership
- Register the software inside the operating system

This complete process is called **Package Management**.

A package manager is a tool that automates software installation, removal, updating, and management.

---

# What Does "Installation" Mean?

Many beginners think installation simply means copying files.

That is **not true**.

Installation means much more than copying files.

When software is installed, Linux performs multiple tasks automatically.

For example:

- Places executable files in the correct directory
- Places configuration files in the correct directory
- Places documentation files in the correct location
- Sets proper permissions
- Assigns the correct owner and group
- Registers the package in the RPM database

Only after completing all these tasks is the software considered properly installed.

---

## Example

Suppose we install Apache Web Server (`httpd`).

Linux automatically performs tasks such as:

```
Executable files
        ↓
/usr/bin/
/usr/sbin/

Configuration files
        ↓
/etc/httpd/

Documentation
        ↓
/usr/share/doc/

Libraries
        ↓
/usr/lib64/

System services
        ↓
systemd
```

The user does not manually move these files.

The Package Manager handles everything.

---

# Installation in Windows vs Linux

## Windows

Most Windows users install software like this:

```
Download setup.exe

↓

Double Click

↓

Next

↓

Next

↓

Finish
```

Everything happens through a GUI (Graphical User Interface).

Examples:

- Chrome
- VLC
- VS Code

---

## Linux Server

Linux servers usually do not have GUI.

Everything is done using CLI (Command Line Interface).

Example:

```
dnf install httpd

or

rpm -ivh package.rpm
```

Instead of clicking buttons, we execute commands.

This is why Linux administrators must understand Package Managers.

---

# Where Does Linux Store Installed Files?

Linux follows a fixed filesystem hierarchy.

Different types of files are stored in different directories.

| File Type | Common Location |
|------------|-----------------|
| Configuration Files | `/etc` |
| Executable Commands | `/usr/bin`, `/usr/sbin` |
| Libraries | `/usr/lib`, `/usr/lib64` |
| Documentation | `/usr/share/doc` |
| Manual Pages | `/usr/share/man` |

Example:

Installing Apache creates:

```
/etc/httpd/

/usr/sbin/httpd

/usr/lib64/

/usr/share/doc/httpd
```

Everything is organized automatically.

---

# RPM (Red Hat Package Manager)

Red Hat-based Linux distributions use packages with the **`.rpm`** extension.

RPM stands for:

> **Red Hat Package Manager**

RPM was introduced around **1995** and became the standard package format for Red Hat-based operating systems.

Examples:

- RHEL
- Rocky Linux
- AlmaLinux
- CentOS

---

# Understanding RPM Package Names

An RPM filename contains important information.

Example:

```text
ansible-core-2.14.2-1.el9.x86_64.rpm
```

Let's break it down.

---

## 1. Package Name

```
ansible-core
```

This is the software name.

Examples:

- httpd
- nginx
- docker
- git
- vim

---

## 2. Version

```
2.14.2
```

This tells us the software version.

Example:

```
Docker 24

Git 2.42

Apache 2.4
```

Newer versions generally include:

- New features
- Bug fixes
- Security patches

---

## 3. Release

```
1.el9
```

This indicates which Enterprise Linux version the package was built for.

Examples:

```
el8

el9
```

Meaning:

```
Enterprise Linux 8

Enterprise Linux 9
```

---

## 4. Architecture

Example:

```
x86_64
```

Architecture tells us which processor the package supports.

Common architectures:

| Architecture | Description |
|--------------|-------------|
| x86_64 | 64-bit Intel/AMD processors |
| aarch64 | ARM 64-bit processors |
| arm64 | Apple Silicon / ARM systems |

Installing the wrong architecture usually results in installation failure.

---

## 5. Extension

All Red Hat packages end with:

```
.rpm
```

Example:

```
docker.rpm

git.rpm

httpd.rpm
```

---

# Types of Package Managers

Linux has evolved over many years.

Three major package management tools were introduced.

---

## 1. RPM

Oldest package manager.

Used mainly for:

- Install
- Remove
- Query

Does **not** automatically resolve dependencies.

---

## 2. YUM

YUM stands for:

> **Yellowdog Updater Modified**

Introduced to simplify package management.

Advantages:

- Dependency resolution
- Repository support
- Automatic package search
- Easier installation

---

## 3. DNF

DNF stands for:

> **Dandified YUM**

Modern replacement for YUM.

Features:

- Faster
- Better dependency management
- Improved performance
- More reliable package handling

Modern RHEL systems primarily use DNF.

---

# RPM Command

RPM is a low-level package management tool.

Unlike DNF or YUM, RPM cannot search repositories automatically.

It requires the **actual RPM file**.

Example:

```
rpm -ivh /root/httpd.rpm
```

Notice the absolute path.

---

# Query Commands

RPM provides many options to inspect installed packages.

---

## Check Whether a Package is Installed

```bash
rpm -q httpd
```

Example output:

```
httpd-2.4.57
```

If not installed:

```
package httpd is not installed
```

---

# Detailed Package Information

```bash
rpm -qi httpd
```

Shows information such as:

- Name
- Version
- Release
- Architecture
- Install Date
- Build Date
- License
- Vendor
- Description

Useful when gathering software details.

---

# List All Installed Packages

```bash
rpm -qa
```

Displays every installed RPM package on the system.

Example:

```
bash

vim

httpd

openssl

kernel

git
```

---

# View Configuration Files

```bash
rpm -qc httpd
```

Shows configuration files associated with the package.

Example:

```
/etc/httpd/conf/httpd.conf
```

Configuration files are generally stored under `/etc`.

---

# View Documentation Files

```bash
rpm -qd httpd
```

Displays documentation such as:

- README
- Release Notes
- Manual Pages

Useful when learning new software.

---

# List Package Files

```bash
rpm -ql httpd
```

Displays every file installed by the package.

Example:

```
/usr/sbin/httpd

/etc/httpd

/usr/share/doc/httpd

/usr/lib64
```

This helps locate binaries, libraries, configuration files, and documentation.

---

# Installing RPM Packages

Syntax:

```bash
rpm -ivh package.rpm
```

Meaning of options:

| Option | Meaning |
|----------|----------|
| `-i` | Install |
| `-v` | Verbose (show detailed output) |
| `-h` | Display progress using hash (`#`) symbols |

Example:

```bash
rpm -ivh httpd.rpm
```

---

# Removing Packages

Syntax:

```bash
rpm -e package-name
```

Example:

```bash
rpm -e httpd
```

The `-e` option stands for **Erase**, which removes the package from the system.

---

# Upgrading Packages

Syntax:

```bash
rpm -Uvh package.rpm
```

Where:

- `-U` → Upgrade (or update to a newer package)
- `-v` → Verbose output
- `-h` → Progress display

Example:

```bash
rpm -Uvh httpd-new.rpm
```

If an older version exists, RPM replaces it with the newer one.

---

# ISO Images and Offline Repositories

A RHEL ISO file is typically **8–10 GB** because it includes the operating system and thousands of RPM packages.

This allows software installation even without an internet connection.

---

## Mounting the ISO

After attaching the ISO to a virtual machine, you can identify the mounted device using:

```bash
lsblk
```

A common mount location is:

```text
/run/media/root/RHEL-9-...
```

Once mounted, the ISO contains structured repositories.

---

## BaseOS Repository

The **BaseOS** directory contains core operating system components, including:

- Kernel packages
- Essential system libraries
- Core utilities
- Boot-related packages

These packages are required for the operating system itself.

---

## AppStream Repository

The **AppStream** directory contains application software such as:

- Firefox
- Apache (`httpd`)
- Nginx
- Database servers
- Development tools
- Programming languages

Think of AppStream as the collection of software you install **on top of** the operating system.

---

# Downloading Packages Using `wget`

Linux servers often do not have a graphical web browser.

To download files from the command line, we use:

```bash
wget
```

Syntax:

```bash
wget <URL>
```

Example:

```bash
wget https://example.com/httpd.rpm
```

The downloaded file is saved in the **current working directory**.

---

# Binary Packages vs Source Packages

There are two common types of software packages.

## Binary Package

Already compiled and ready to install.

Example:

```
httpd.rpm
```

You simply install it.

---

## Source Package

Contains the source code.

It allows developers to:

- Read the code
- Modify it
- Compile it
- Build a customized version

Source packages are mainly used by developers, while system administrators usually work with binary packages.

---

# Finding RPM Packages Online

A popular website for locating RPM packages is:

- **pkgs.org**

It provides RPM packages for various Linux distributions and versions, making it useful when a package is not available locally.

---

# Update vs Upgrade

These terms are often used interchangeably, but they generally refer to different scopes of change.

### Update

An update usually includes:

- Security patches
- Bug fixes
- Minor improvements

The software version generally remains within the same release family.

---

### Upgrade

An upgrade usually involves:

- A newer software version
- New features
- Major changes
- Possible dependency changes

For example:

```
Version 1.x
      ↓
Version 2.x
```

---

# Package Compatibility Rules

Compatibility depends on the operating system version.

A general rule is:

- Packages built for an **older Enterprise Linux version** (for example, EL8) often work on a **newer version** (EL9), because newer systems usually maintain backward compatibility.
- Packages built for a **newer version** (EL9) may fail on an **older version** (EL8) if they require newer libraries, drivers, or kernel features that do not exist on the older system.

**Examples:**

- ✅ EL8 package → May work on EL9.
- ❌ EL9 package → Likely to fail on EL8.

Always choose a package that matches your operating system version and CPU architecture whenever possible.

---

# Summary

In this session, we learned:

- What Package Management is and why it is important.
- What software installation actually does in Linux.
- How Linux organizes installed files using the filesystem hierarchy.
- The structure of an RPM package filename.
- The three major package managers: **RPM**, **YUM**, and **DNF**.
- How to use the `rpm` command to query, install, remove, and upgrade packages.
- The purpose of **BaseOS** and **AppStream** repositories in a RHEL ISO.
- How to download RPM packages using `wget`.
- The difference between binary and source packages.
- The difference between an update and an upgrade.
- Basic package compatibility rules across Enterprise Linux versions.

This knowledge forms the foundation for working with software management on Red Hat–based Linux systems. In the next part, you'll typically build on these concepts by configuring repositories and using higher-level package managers such as **YUM** and **DNF** for dependency-aware software installation.
