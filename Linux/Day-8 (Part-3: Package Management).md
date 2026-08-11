# Linux Package Management – Part 3

## 1. Introduction to Software Installation

In Linux, software installation does not simply mean downloading a file and opening it.

When we install software, the system has to perform several tasks:

1. Put the required files in their correct locations.
2. Set the required file permissions.
3. Set the correct ownership.
4. Place configuration files in the appropriate directories.
5. Make required commands or binaries available to the system.
6. Track the installed software so that it can later be updated, queried, or removed.

For example, when we install a command such as `wget`, Linux may place:

* The executable command under `/usr/bin/`
* Configuration files under `/etc/`
* Documentation in appropriate documentation directories
* Package information in the RPM database

For example:

```bash
/usr/bin/wget
/etc/wgetrc
```

So, **software installation in Linux is basically the proper placement and configuration of software files inside the Linux filesystem.**

---

# 2. Linux CLI Installation vs Windows GUI Installation

Most Windows users are familiar with GUI-based installation.

For example:

```text
Download software
       ↓
Double-click setup.exe
       ↓
Next
       ↓
Next
       ↓
Install
       ↓
Finish
```

The installer automatically handles many things in the background.

Linux servers are different.

Most enterprise Linux servers are managed through the **Command Line Interface (CLI)**.

There may be no graphical desktop or browser available.

Therefore, administrators use commands and package managers to install and manage software.

For example:

```bash
rpm -ivh package.rpm
```

or:

```bash
yum install wget
```

The package manager takes care of installing the required files into the correct locations.

---

# 3. Linux Filesystem and Software Installation

Linux follows a standard filesystem structure.

Different types of software files are normally stored in different directories.

### `/etc`

This directory contains system and application configuration files.

For example:

```bash
/etc/wgetrc
```

### `/usr/bin`

This directory commonly contains executable commands.

For example:

```bash
/usr/bin/wget
```

After installing a package, we can use RPM commands to identify where its files have been placed.

For example:

```bash
rpm -ql wget
```

This command displays the files installed by the `wget` package.

---

# 4. What is RPM?

**RPM** stands for **Red Hat Package Manager**.

RPM is the low-level package management system used by Red Hat-based Linux distributions.

RPM packages normally have the extension:

```text
.rpm
```

For example:

```text
wget-1.21.3-1.el9.x86_64.rpm
```

An RPM package contains the files required to install a particular piece of software along with package metadata.

---

# 5. Understanding RPM Package Naming

An RPM package name generally follows this structure:

```text
PackageName-Version-Release.Architecture.rpm
```

Example:

```text
wget-1.21.3-1.el9.x86_64.rpm
```

Let's break it down.

### Package Name

```text
wget
```

This is the name of the software.

Another example could be:

```text
ansible-core
```

---

### Version

```text
1.21.3
```

This represents the software version released by the software developers.

---

### Release

```text
1.el9
```

This identifies the package release/build information for the particular Enterprise Linux ecosystem.

---

### Architecture

```text
x86_64
```

This indicates the processor architecture for which the package was built.

Common architectures include:

```text
x86_64
aarch64
```

`x86_64` is commonly used on 64-bit Intel/AMD systems.

`aarch64` is used for 64-bit ARM systems.

---

### Extension

```text
.rpm
```

This tells us that the file is an RPM package.

So:

```text
wget-1.21.3-1.el9.x86_64.rpm
```

means:

```text
Software     → wget
Version      → 1.21.3
Release      → 1.el9
Architecture → x86_64
Package type → rpm
```

---

# 6. Package and Operating System Compatibility

When installing an RPM package, we should consider two important things:

1. Operating system version
2. CPU architecture

For example, suppose we have:

```text
RHEL 9
```

and the package is built for:

```text
x86_64
```

The package architecture must match the machine architecture.

We can check the system architecture using:

```bash
uname -m
```

Example:

```text
x86_64
```

---

## Important Note About EL8 and EL9

A package built for an older Enterprise Linux release may sometimes work on a newer release, depending on its dependencies and compatibility.

However, the opposite direction is much more likely to cause problems.

For example:

```text
EL8 package → EL9
```

may work in some cases.

But:

```text
EL9 package → EL8
```

can fail because the older operating system may not provide the required libraries, APIs, or other dependencies.

Therefore, **we should always prefer a package built specifically for our operating system version and architecture.**

---

# 7. Update vs Upgrade

These two terms are often confused.

## Update

An update normally means applying a newer release within the same major software or OS family.

For example:

```text
2.14 → 2.14.2
```

The software remains fundamentally the same, but bugs may be fixed and security improvements may be added.

Updates can include:

* Bug fixes
* Security patches
* Minor improvements
* Performance improvements

---

## Upgrade

An upgrade generally means moving to a newer major version.

For example:

```text
RHEL 8 → RHEL 9
```

A major upgrade can introduce:

* New features
* New libraries
* Architectural changes
* New system components
* Compatibility changes

So remember:

```text
Update  → Smaller/minor changes
Upgrade → Major version change
```

---

# 8. Generations of Linux Package Managers

Red Hat-based Linux systems have evolved through different package management tools.

The important generations are:

```text
RPM
 ↓
YUM
 ↓
DNF
```

Each generation solved limitations of the previous approach.

---

# 9. RPM – Low-Level Package Manager

RPM is the basic/low-level package management tool.

It is very good at:

* Installing RPM packages
* Removing packages
* Querying package information
* Tracking installed files
* Checking package metadata

However, RPM has an important limitation.

### RPM does not automatically resolve dependencies.

Suppose package `A` requires:

```text
Package B
Package C
Package D
```

If we use RPM directly, we may need to obtain those dependencies ourselves.

RPM does not behave like a high-level repository-aware package manager.

For example:

```bash
rpm -ivh package.rpm
```

If dependencies are missing, RPM can report dependency errors.

---

# 10. YUM

**YUM** stands for:

```text
Yellowdog Updater, Modified
```

YUM was introduced to make package management easier.

Instead of manually finding every dependency, YUM can:

1. Search configured repositories.
2. Find the required package.
3. Find dependencies.
4. Download required packages.
5. Install them.

For example:

```bash
yum install wget
```

This is much easier than manually finding an RPM file and all of its dependencies.

---

# 11. DNF

**DNF** stands for:

```text
Dandified YUM
```

DNF is the modern package management tool used in current Red Hat-based systems.

It provides repository management and dependency resolution while improving upon the older YUM implementation.

For example:

```bash
dnf install wget
```

The basic relationship can be remembered as:

```text
RPM
 ↓
Low-level package management

YUM
 ↓
High-level package management

DNF
 ↓
Modern high-level package management
```

---

# 12. RHEL Installation ISO as a Local Repository

A RHEL installation ISO is not only used for installing the operating system.

It can also contain a large collection of software packages.

This is useful in environments where the server does not have internet access.

For example:

```text
RHEL ISO
   │
   ├── BaseOS
   │
   └── AppStream
```

The ISO can therefore act as a **local software source/repository**.

---

# 13. Finding the Attached ISO

When an ISO is attached to a virtual machine, Linux detects the associated block device.

We can use:

```bash
lsblk
```

`lsblk` means:

```text
List Block Devices
```

It helps us identify:

* Disks
* Partitions
* Optical devices
* Their mount points

Depending on the environment, the ISO may be mounted somewhere under:

```text
/run/media/
```

For example:

```text
/run/media/root/RHEL-9-...
```

The exact path depends on the system and how the ISO was attached/mounted.

---

# 14. BaseOS

Inside the RHEL installation media, one important repository is:

```text
BaseOS
```

BaseOS contains packages required for the basic operating system environment.

It includes foundational components such as:

* Core system packages
* Kernel-related packages
* Basic system utilities
* Fundamental operating system components

Think of it as:

```text
BaseOS = Foundation of the operating system
```

---

# 15. AppStream

The second important repository is:

```text
AppStream
```

AppStream contains additional applications and user-space components.

Examples include:

* Application software
* Development tools
* Server applications
* Additional utilities

For example:

```text
httpd
nginx
```

and many other application packages can be provided through AppStream repositories.

Think of it as:

```text
AppStream = Applications and additional software
```

---

# 16. Installing an RPM Directly from ISO

Because RPM is a low-level package manager, it does not automatically search the ISO for a package.

Therefore, we need to provide the package's exact path.

For example:

```bash
rpm -ivh /run/media/root/RHEL-9-*/AppStream/Packages/w/wget-*.rpm
```

The exact filename and directory can vary.

The important point is:

> **RPM needs the package file path when installing a local RPM package.**

This is different from:

```bash
dnf install wget
```

where DNF can search configured repositories.

---

# 17. Important RPM Commands

RPM provides several options for querying and managing packages.

---

## Check Whether a Package Is Installed

```bash
rpm -q wget
```

Here:

```text
-q = query
```

If the package is installed, RPM displays its package information.

If it is not installed, RPM reports that the package is not installed.

---

## Display Detailed Package Information

```bash
rpm -qi wget
```

Here:

```text
-q  = query
-i  = information
```

This displays information such as:

* Package name
* Version
* Release
* Architecture
* Installation date
* License
* Description

---

## List All Installed Packages

```bash
rpm -qa
```

Here:

```text
-q = query
-a = all
```

This displays all packages currently installed on the system.

Because a Linux system can contain a very large number of packages, the output can be long.

---

## Find Configuration Files

```bash
rpm -qc wget
```

Here:

```text
-q = query
-c = config
```

This displays configuration files belonging to the package.

For example:

```text
/etc/wgetrc
```

---

## List All Files Installed by a Package

```bash
rpm -ql wget
```

Here:

```text
-q = query
-l = list
```

This is particularly useful when you want to know:

> Where did this package put its files?

---

## Find Documentation Files

```bash
rpm -qd wget
```

Here:

```text
-q = query
-d = documentation
```

This displays documentation files associated with the package.

---

# 18. Removing a Package

To remove a package:

```bash
rpm -e wget
```

Here:

```text
-e = erase
```

The package is removed from the system.

For more visible output, we can use:

```bash
rpm -evh wget
```

Where:

```text
-e = erase
-v = verbose
-h = show hash/progress
```

The `-h` option displays progress using hash marks:

```text
###########
```

---

# 19. Installing an RPM Package

To install a local RPM package:

```bash
rpm -ivh package.rpm
```

Here:

```text
-i = install
-v = verbose
-h = hash/progress
```

Example:

```bash
rpm -ivh /path/to/wget-package.rpm
```

The package must exist at the specified path.

---

# 20. Updating or Upgrading an RPM Package

The RPM command:

```bash
rpm -Uvh package.rpm
```

is used to upgrade an installed package using the supplied RPM file.

Here:

```text
-U = upgrade
-v = verbose
-h = hash/progress
```

Example:

```bash
rpm -Uvh /path/to/new-package.rpm
```

RPM checks the package being provided and performs the appropriate upgrade operation.

---

# 21. RPM Command Cheat Sheet

| Command             | Meaning              | Purpose                            |
| ------------------- | -------------------- | ---------------------------------- |
| `rpm -q package`    | Query                | Check whether package is installed |
| `rpm -qi package`   | Query Info           | Show package information           |
| `rpm -qa`           | Query All            | List all installed packages        |
| `rpm -qc package`   | Query Config         | Show configuration files           |
| `rpm -ql package`   | Query List           | Show all installed files           |
| `rpm -qd package`   | Query Documentation  | Show documentation files           |
| `rpm -e package`    | Erase                | Remove package                     |
| `rpm -evh package`  | Erase Verbose Hash   | Remove with progress               |
| `rpm -ivh file.rpm` | Install Verbose Hash | Install local RPM                  |
| `rpm -Uvh file.rpm` | Upgrade Verbose Hash | Upgrade using local RPM            |

---

# 22. Downloading Software from the Command Line

Linux servers are often **headless servers**.

That means there may be no:

* GUI
* Web browser
* Desktop environment

So administrators need command-line tools to download files.

One commonly used command is:

```bash
wget
```

---

## Basic wget Syntax

```bash
wget URL
```

Example:

```bash
wget https://example.com/package.rpm
```

The file is downloaded into the current working directory.

We can verify the downloaded file using:

```bash
ls
```

---

# 23. Finding RPM Packages Online

Package indexes such as `pkgs.org` can be used to search for packages.

When selecting an RPM package, always check:

```text
Distribution
Version
Architecture
Package version
```

For example, if the server is:

```text
RHEL 9
x86_64
```

we should look for a package compatible with:

```text
EL9
x86_64
```

Do not simply download the first RPM you find.

---

# 24. Binary Package vs Source Package

There are two important types of software packages.

## Binary Package

A binary package contains software that has already been compiled.

For example:

```text
application.rpm
```

The administrator can normally install it directly.

The basic flow is:

```text
Download RPM
      ↓
Install RPM
      ↓
Run Application
```

---

## Source Package

A source package contains the source code of the software.

The administrator may need to:

```text
Download source
      ↓
Configure
      ↓
Compile
      ↓
Install
```

Source packages are useful when we need:

* Custom compilation
* Specific build options
* Modifications
* A version not available as a suitable binary package

For normal enterprise package management, binary packages managed through repositories are generally preferred.

---

# 25. Complete Learning Flow

The complete concept covered in this session can be remembered like this:

```text
Software
   ↓
Installation
   ↓
Files + Permissions + Ownership
   ↓
RPM Package
   ↓
RPM
   ↓
Dependency Problem
   ↓
YUM
   ↓
Modern Replacement
   ↓
DNF
   ↓
Repositories
   ↓
BaseOS + AppStream
```

And when working manually with an RPM:

```text
Find package
     ↓
Check OS version
     ↓
Check architecture
     ↓
Download/locate RPM
     ↓
Install using rpm -ivh
     ↓
Verify using rpm -q
     ↓
Check files using rpm -ql
     ↓
Check configuration using rpm -qc
```

---

# 26. Practical Commands to Remember

### Check architecture

```bash
uname -m
```

### Check block devices

```bash
lsblk
```

### Check installed package

```bash
rpm -q wget
```

### Get package information

```bash
rpm -qi wget
```

### List all installed packages

```bash
rpm -qa
```

### Find configuration files

```bash
rpm -qc wget
```

### List package files

```bash
rpm -ql wget
```

### Find documentation

```bash
rpm -qd wget
```

### Remove package

```bash
rpm -e wget
```

### Install RPM

```bash
rpm -ivh package.rpm
```

### Upgrade RPM

```bash
rpm -Uvh package.rpm
```

### Download a file

```bash
wget URL
```

---

# 27. Key Points for Revision

Before moving to the next package-management session, make sure these points are clear:

1. **Software installation** means placing software files correctly and configuring their permissions/ownership.
2. Linux servers commonly use the **CLI** for software management.
3. RHEL software packages normally use the **`.rpm`** extension.
4. RPM filenames contain useful information about the **package, version, release, and architecture**.
5. `x86_64` and `aarch64` represent different CPU architectures.
6. Always consider **OS version and architecture compatibility**.
7. **RPM** is a low-level package manager.
8. RPM does **not automatically resolve dependencies**.
9. **YUM** was developed as a higher-level package manager with dependency resolution.
10. **DNF** is the modern package management tool in current RHEL-based systems.
11. RHEL installation media contains repositories such as **BaseOS** and **AppStream**.
12. `lsblk` helps identify block devices and their mount information.
13. `rpm -q` checks package installation status.
14. `rpm -qi` displays package information.
15. `rpm -ql` shows files installed by a package.
16. `rpm -qc` shows package configuration files.
17. `rpm -qd` shows documentation files.
18. `rpm -e` removes a package.
19. `rpm -ivh` installs a local RPM.
20. `rpm -Uvh` upgrades a local RPM.
21. `wget` is useful for downloading files directly from the command line.
22. **Binary packages** are already compiled; **source packages** contain source code that can be compiled manually.

This forms the **complete Part 1 foundation**. Part 2 naturally builds on this by going deeper into **YUM/DNF, repositories, dependency resolution, repository configuration, and practical package installation using repositories**.
