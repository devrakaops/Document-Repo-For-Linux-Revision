# Linux Training – Day 1 (Part 1)
<img width="1062" height="192" alt="image" src="https://github.com/user-attachments/assets/54c5b2c7-293e-4736-a00f-c24627492fba" />

# Understanding the Linux Operating System

---

# Learning Objectives

After completing this session, you should be able to:

* Understand what an Operating System (OS) is.
* Understand the purpose of an Operating System.
* Identify the major components of a Linux Operating System.
* Understand why Linux is widely used in the IT industry.
* Understand the Linux Operating System Architecture.
* Understand the role of the Shell.
* Understand the role of the Kernel.
* Understand the purpose of Device Drivers.
* Understand how a user request reaches the hardware.
* Understand how Linux communicates with hardware internally.

---

# Introduction

Before installing Linux or learning Linux commands, it is important to understand what an Operating System actually is.

Many beginners start learning Linux commands directly, but without understanding how Linux works internally, those commands become difficult to understand.

Think of it like driving a car.

Anyone can learn how to drive a car by pressing the accelerator, brake, and clutch. However, if you also understand how the engine works, you can troubleshoot problems more easily.

Similarly, before learning Linux commands, we should understand how Linux works internally.

---

# What is an Operating System?

An **Operating System (OS)** is a system software that manages the computer's hardware and software resources. It acts as an interface between the **user** and the **computer hardware**, allowing users to interact with the computer and run applications.

In simple words,

> **An Operating System is software that controls the entire computer.**

Without an operating system, a computer is simply a collection of hardware components such as the CPU, RAM, hard disk, keyboard, and monitor. These components cannot perform useful tasks on their own.

The operating system brings all these hardware components together and allows them to work as one complete system.

Some common operating systems are:

* Linux
* Microsoft Windows
* macOS
* UNIX
* Android
* iOS

Although all these operating systems perform the same basic job, they are designed for different purposes.

For example:

* Windows is commonly used on personal computers.
* macOS is designed for Apple devices.
* Android is designed for smartphones.
* Linux is widely used on servers, cloud platforms, and supercomputers.

---

# Why Do We Need an Operating System?

Imagine purchasing a new laptop that contains:

* CPU
* RAM
* SSD
* Keyboard
* Mouse
* Monitor

Now imagine that no operating system is installed.

Can you open Google Chrome?

No.

Can you create a file?

No.

Can you install software?

No.

Can you watch videos?

No.

Although the hardware is present, it cannot perform any useful task because there is no software to manage it.

The operating system makes the hardware usable.

It allows users to:

* Install software
* Run applications
* Store files
* Access the internet
* Connect external devices
* Perform day-to-day tasks

Without an operating system, the computer cannot be used effectively.

---

# Responsibilities of an Operating System

An Operating System performs many important tasks behind the scenes.

Some of its major responsibilities include:

## Process Management

Whenever multiple applications are running simultaneously, the operating system decides which process should receive CPU time and in what order.

For example:

* Google Chrome
* VS Code
* VLC Media Player
* Terminal

All these applications may be running at the same time. The operating system manages them efficiently so that the user experiences smooth performance.

---

## Memory Management

Every application requires RAM.

The operating system allocates memory to applications and releases that memory when the application is closed.

Without memory management, applications would overwrite each other's data and the system would become unstable.

---

## CPU Management

The CPU is one of the most valuable resources in a computer.

The operating system decides:

* Which process should run first.
* How much CPU time each process receives.
* Which process should wait.

This process is known as **CPU Scheduling**.

---

## File Management

The operating system manages all files and directories.

Examples include:

* Creating files
* Deleting files
* Renaming files
* Moving files
* Organizing directories
* Controlling file permissions

---

## Device Management

The operating system communicates with hardware devices such as:

* Keyboard
* Mouse
* Printer
* USB Devices
* Hard Disk
* SSD
* Network Card

Without the operating system, applications cannot communicate directly with hardware.

---

## Security

The operating system provides security by:

* Managing user accounts
* Managing passwords
* Controlling permissions
* Preventing unauthorized access

---

## Network Management

The operating system manages network communication.

It allows applications to communicate over the internet and local networks.

Examples include:

* Browsing websites
* Sending emails
* Connecting to servers
* Downloading files

---

# Components of a Linux Operating System

Many beginners think that Linux means only the Kernel.

This is **not correct**.

The Kernel is only one component of the Linux Operating System.

A complete Linux Operating System consists of multiple components working together.

```
Linux Operating System

├── Kernel
├── Shell
├── System Libraries
├── System Utilities
├── Device Drivers
├── File System
├── Configuration Files
└── Application Programs
```

Each component has a different responsibility.

Together, they form the complete operating system.

Therefore,

> **Operating System ≠ Kernel**

Instead,

> **Operating System = Kernel + Shell + Drivers + Libraries + Utilities + File System + Applications**

This is one of the most important concepts to remember.

---

# Why is Linux Popular?

Linux is the most widely used operating system in the IT industry.

Today, most web servers, cloud platforms, Kubernetes clusters, Docker hosts, and enterprise applications run on Linux.

There are several reasons behind its popularity.

---

## 1. Open Source

Linux is an Open Source operating system.

This means its source code is publicly available.

Anyone can study it, improve it, or contribute to it.

Since Linux does not require expensive licensing like many commercial operating systems, companies can significantly reduce their infrastructure costs.

---

## 2. Security

Linux has a strong security model.

It provides:

* User-based access control
* File permissions
* Process isolation
* Secure authentication

These features make Linux highly secure for enterprise environments.

---

## 3. Performance

Linux consumes fewer system resources than many desktop operating systems.

Because of this, applications receive more CPU and memory resources, resulting in better performance.

---

## 4. Stability

Linux systems are known for their stability.

Production Linux servers can continue running for months or even years without requiring frequent reboots.

This makes Linux an excellent choice for critical business applications.

---

## 5. Flexibility

Linux can be installed on:

* Personal laptops
* Servers
* Virtual Machines
* Cloud platforms
* Embedded devices
* Supercomputers

Its flexibility allows it to be used in almost every computing environment.

---

# Linux Operating System Architecture

Now that we understand what an Operating System is, let's understand how Linux works internally.

The Linux Operating System is organized into multiple layers.

Each layer has a specific responsibility.

When a user performs any action, the request travels through these layers until it reaches the hardware.

The response then follows the same path back to the user.

<img width="730" height="342" alt="image" src="https://github.com/user-attachments/assets/a57d719d-0724-487a-8ef7-19fe1cdf23e7" />



The architecture looks like this.

```
                    User
                      │
                      ▼
             Application Layer
(Paint, VS Code, Chrome, VLC,
 Terminal, Calculator etc.)
                      │
                      ▼
          Shell / System Libraries
        (bash, sh, zsh, ksh ...)
                      │
                      ▼
          Linux Kernel (Core of OS)
                      │
                      ▼
             Device Drivers Layer
(Network Driver, USB Driver,
Disk Driver, Printer Driver etc.)
                      │
                      ▼
               Hardware Layer
 CPU, RAM, SSD, Keyboard,
 Mouse, Printer, Network Card
```

Let us understand each layer one by one.

---

# User Layer

The User Layer is the topmost layer.

This is where a person interacts with the computer.

Users perform actions such as:

* Opening Google Chrome
* Watching videos
* Creating files
* Browsing folders
* Running Linux commands
* Printing documents

The user does **not** communicate directly with the hardware.

Instead, every request is passed to the operating system.

---

# Application Layer

The Application Layer contains all the software programs that users work with.

Examples include:

* Google Chrome
* Firefox
* VS Code
* LibreOffice
* VLC Media Player
* Calculator
* Terminal
* File Manager

Applications provide a user-friendly interface.

However, applications cannot directly access the CPU, RAM, or hard disk.

Whenever an application needs hardware resources, it asks the operating system.

For example,

When Google Chrome wants to save a downloaded file, it cannot write directly to the SSD.

Instead, it requests the operating system to perform the task.

---

# Shell Layer

The **Shell** is one of the most important components of Linux.

It acts as a communication interface between the user and the Kernel.

The Shell receives commands from the user, interprets them, and passes them to the Kernel.

For this reason, the Shell is often called the **Command Interpreter**.

Some common Linux shells are:

* Bourne Shell (sh)
* Bourne Again Shell (bash)
* Korn Shell (ksh)
* Z Shell (zsh)

Among these, **bash** is the most commonly used shell in Linux.

### Example

Suppose the user types the following command:

```bash
pwd
```

The Shell receives this command.

It understands that the user wants to display the current working directory.

The Shell sends the request to the Kernel.

The Kernel retrieves the required information.

The Shell then displays the output on the screen.

Notice that the Shell itself does not know where the directory is stored.

Its job is simply to interpret the user's command and communicate with the Kernel.

---

# Linux Kernel

The **Kernel** is the heart of the Linux Operating System.

It is the core component responsible for managing the entire system.

Unlike applications, the Kernel has direct access to the hardware.

Every request made by applications eventually reaches the Kernel.

The Kernel decides how the hardware should perform the requested task.

Because of this, the Kernel is often referred to as the **Brain of the Operating System**.

---

# Responsibilities of the Kernel

The Kernel performs many critical functions.

### Process Management

Creates, schedules, and terminates processes.

### Memory Management

Allocates and releases RAM.

### CPU Scheduling

Determines which process receives CPU time.

### File System Management

Reads and writes files on storage devices.

### Device Management

Communicates with hardware devices.

### Network Management

Handles network communication.

### Security

Enforces permissions and user access control.

Without the Kernel, the operating system cannot function.

---

# Device Drivers

The Kernel communicates with hardware using **Device Drivers**.

A Device Driver is a software component that allows the operating system to communicate with a specific hardware device.

Examples include:

* Keyboard Driver
* Mouse Driver
* USB Driver
* Network Driver
* Printer Driver
* Graphics Driver
* Storage Driver

Each hardware device has its own driver.

Without the correct driver, the operating system cannot use that hardware properly.

For example,

If the printer driver is missing, the operating system cannot print documents even though the printer is connected.

---

# Hardware Layer

The Hardware Layer contains the physical components of the computer.

Examples include:

* CPU
* RAM
* Hard Disk
* SSD
* Keyboard
* Mouse
* Printer
* Monitor
* Network Card

The hardware does not understand human language.

It executes only machine-level instructions provided by the Kernel.

---

# Complete Request Flow

Now let us understand how all these layers work together.

Suppose a user wants to create a new file using the following command.

```bash
touch notes.txt
```

Internally, the request flows like this.

```
User

↓

Shell (bash)

↓

Kernel

↓

Storage Driver

↓

SSD / Hard Disk

↓

Kernel

↓

Shell

↓

User
```

The user simply types one command.

However, internally, multiple components work together to complete the task.

---

# Another Real-Life Example

Suppose a user wants to print a document.

The internal communication looks like this.

```
User

↓

LibreOffice Writer

↓

Operating System

↓

Kernel

↓

Printer Driver

↓

Printer

↓

Document Printed
```

The user never communicates directly with the printer.

Everything is managed by the operating system.

---

# How Does Linux Communicate with Hardware?

Computers cannot understand English, Hindi, or any human language.

They understand only **Machine Language**, which is represented using binary values.

Binary consists of only two digits.

```
0
1
```

When a user gives a command, the following steps occur:

1. The user enters a command.
2. The Shell interprets the command.
3. The Kernel processes the request.
4. The Kernel communicates with the appropriate device driver.
5. The hardware executes the operation.
6. The result is returned to the user.

This entire process happens within milliseconds.

Although the user sees only the final result, multiple layers of the Linux Operating System work together behind the scenes.

---

<br>
<br>
<img width="1067" height="193" alt="image" src="https://github.com/user-attachments/assets/dd0d90f8-7a28-4ef3-a80b-252761b1c41a" />

<br>
<br>

---

# Linux Training – Day 1 (Part 2)

# Virtualization, Hypervisors and Installing Red Hat Enterprise Linux (RHEL)

---

# Introduction

In the previous session, we understood what an Operating System is, how Linux is organized internally, and how the Kernel communicates with the hardware.

Now a practical question arises.

Suppose you are using a Windows laptop, but you want to learn Linux.

Do you need to remove Windows completely and install Linux?

The answer is **No**.

Instead of replacing your existing operating system, you can create another computer inside your computer. This is possible because of a technology called **Virtualization**.

Virtualization is one of the most important technologies in modern IT. Today, cloud platforms, enterprise data centers, and almost every DevOps environment use virtualization in some form.

---

# Why Was Virtualization Introduced?

Earlier, organizations followed the **One Server – One Operating System – One Application** approach.

For example,

```text
Server 1 → Windows → Payroll Application

Server 2 → Linux → Database

Server 3 → Linux → Web Server

Server 4 → Windows → Mail Server
```

Although every server was dedicated to a single application, most of the hardware resources remained unused.

For example:

* CPU Usage → 10%
* RAM Usage → 20%
* Storage Usage → 15%

This meant companies had to purchase many physical servers even though most of their computing power was wasted.

Problems with this approach included:

* High hardware cost
* High electricity consumption
* More cooling requirements
* More rack space in data centers
* Increased maintenance
* Difficult backup and recovery

To solve these problems, virtualization was introduced.

---

# What is Virtualization?

**Virtualization** is a technology that allows multiple virtual computers to run on a single physical computer.

Each virtual computer is called a **Virtual Machine (VM)**.

Every virtual machine has its own:

* Operating System
* CPU allocation
* RAM allocation
* Storage allocation
* Network configuration

Although all virtual machines share the same physical hardware, each one behaves like an independent computer.

---

# Understanding Virtualization with an Example

Suppose you have the following laptop.

```text
Laptop

CPU : 8 Cores

RAM : 16 GB

Storage : 500 GB SSD
```

Without virtualization, you can install only one operating system.

For example:

```text
Laptop

↓

Windows 11
```

If you also want to learn Linux, you would normally have to remove Windows and install Linux.

Virtualization eliminates this problem.

Now the same laptop can run multiple operating systems simultaneously.

```text
Physical Laptop

│

├── Windows 11 (Host Operating System)

│

├── Ubuntu Virtual Machine

├── Red Hat Virtual Machine

├── CentOS Virtual Machine

└── Kali Linux Virtual Machine
```

Each virtual machine behaves like a completely separate computer.

For example,

* Ubuntu can be restarted independently.
* Red Hat can be shut down without affecting Windows.
* Kali Linux can have different users and software.
* CentOS can have its own IP address.

Even though all virtual machines use the same hardware, they are isolated from one another.

---

# What is a Virtual Machine (VM)?

A **Virtual Machine (VM)** is a software-based computer that behaves exactly like a physical computer.

A virtual machine has its own:

* CPU
* RAM
* Hard Disk
* Network Adapter
* BIOS/UEFI
* Operating System

The only difference is that these hardware components are virtual rather than physical.

To the operating system installed inside the VM, everything appears to be real hardware.

---

# Advantages of Virtualization

Virtualization provides many benefits.

## Better Hardware Utilization

Instead of one application using only a small percentage of the server, multiple virtual machines can efficiently share the hardware.

---

## Reduced Infrastructure Cost

Organizations need fewer physical servers.

This reduces:

* Hardware expenses
* Power consumption
* Cooling costs
* Maintenance costs

---

## Isolation

Every virtual machine is independent.

If one VM crashes, the others continue running normally.

---

## Easy Testing

Developers and administrators can test software without affecting their primary operating system.

For example,

You can install experimental software inside a Linux VM without risking your Windows laptop.

---

## Easy Backup

Virtual machines can be backed up and restored quickly.

Many virtualization platforms even support snapshots.

A snapshot captures the complete state of a virtual machine at a specific point in time.

If something goes wrong, the VM can be restored to that snapshot.

---

## Multiple Operating Systems

A single physical machine can simultaneously run:

* Windows
* Ubuntu
* Red Hat
* CentOS
* Debian

This is especially useful for learning and testing.

---

# What is a Hypervisor?

Virtualization is possible because of a special software called a **Hypervisor**.

A Hypervisor creates and manages virtual machines.

It is responsible for allocating hardware resources such as:

* CPU
* RAM
* Storage
* Network Interfaces

Whenever a virtual machine is created, the Hypervisor decides how much hardware should be assigned to it.

Without a Hypervisor, virtualization cannot exist.

---

# Types of Hypervisors

There are two major types of Hypervisors.

---

# Type-1 Hypervisor (Bare Metal Hypervisor)

A **Type-1 Hypervisor** is installed directly on the physical hardware.

There is no traditional operating system between the Hypervisor and the hardware.

Architecture:

```text
Virtual Machines

├── Linux VM
├── Windows VM
└── Ubuntu VM

        │

Hypervisor
(VMware ESXi)

        │

Physical Hardware
```

Because there is no host operating system, Type-1 Hypervisors provide:

* Better Performance
* Higher Stability
* Better Resource Utilization
* Better Security

They are mainly used in:

* Data Centers
* Cloud Providers
* Enterprise Servers

Examples:

* VMware ESXi
* Microsoft Hyper-V Server
* Citrix XenServer

---

# Type-2 Hypervisor

A **Type-2 Hypervisor** is installed as a normal application on an existing operating system.

Architecture:

```text
Virtual Machines

├── Red Hat
├── Ubuntu
└── CentOS

        │

VMware Workstation

        │

Windows 11

        │

Physical Hardware
```

The Host Operating System manages the hardware first.

The Hypervisor then uses the resources provided by the Host Operating System.

Examples:

* VMware Workstation
* Oracle VirtualBox
* VMware Fusion (macOS)

Type-2 Hypervisors are commonly used for:

* Learning
* Testing
* Software Development
* Personal Labs

In this training, we use **VMware Workstation**, which is a **Type-2 Hypervisor**.

---

# Why Do We Use VMware Workstation?

Instead of installing Linux directly on our laptops, we install Linux inside VMware Workstation.

This approach provides several advantages.

* Windows remains unchanged.
* Linux can be removed at any time.
* Multiple Linux distributions can be installed.
* Easy backup using virtual machine files.
* No risk of damaging the existing operating system.

For beginners, VMware Workstation is one of the safest ways to learn Linux.

---

# Installing Red Hat Enterprise Linux (RHEL)

Now that we understand virtualization, we can install Linux inside a virtual machine.

The instructor demonstrated the installation of **Red Hat Enterprise Linux (RHEL)** using VMware Workstation.

---

# Requirements

Before starting the installation, ensure that the following software is available.

* VMware Workstation
* Red Hat Enterprise Linux ISO Image
* At least 20 GB free storage
* Sufficient RAM
* Hardware Virtualization enabled in BIOS (if required)

---

# What is an ISO Image?

An **ISO Image** is a digital copy of an installation DVD.

Instead of using a physical DVD, VMware boots directly from the ISO file.

The boot process is as follows:

```text
RHEL ISO File

↓

VMware Workstation

↓

Virtual Machine

↓

Linux Installer Starts

↓

Operating System Installed
```

---

# Creating a Virtual Machine

While creating a virtual machine, VMware asks for hardware allocation.

These resources are reserved only while the VM is running.

---

## Disk Space

The instructor recommended allocating at least:

```text
20 GB
```

This provides enough space for:

* Linux Operating System
* Applications
* Practice Files
* Logs

---

## CPU Allocation

Recommended configuration:

```text
Processors : 1

Cores : 1
```

For beginners, one processor with one core is sufficient.

Allocating unnecessary CPU resources may slow down the host operating system.

---

## Memory Allocation

RAM should be allocated according to the available hardware.

For example,

If the laptop has:

```text
16 GB RAM
```

Allocating:

```text
2 GB or 4 GB
```

to the virtual machine is usually sufficient for learning.

Avoid assigning all available memory to the VM, as the host operating system also requires RAM.

---

# Installing the Operating System

After configuring the hardware:

1. Attach the RHEL ISO file.
2. Start the virtual machine.
3. The installer will boot from the ISO.
4. Follow the installation wizard.
5. Configure language, keyboard, and storage settings.
6. Set the Root Password.
7. Create a Local User.
8. Complete the installation.
9. Restart the virtual machine.
10. Log in to Linux.

---

# Root User

During installation, Linux asks you to configure the **Root User**.

The Root User is the administrator of the entire operating system.

Root has unrestricted access to every part of Linux.

The Root User can:

* Install software
* Remove software
* Create users
* Delete users
* Modify configuration files
* Change system settings
* Access every directory
* Manage services
* Shut down or restart the system

Because the Root account has complete control, its password should always be kept secure.

---

# Local User

The installer also allows you to create a **Local User**.

A Local User is intended for day-to-day work.

Unlike the Root User, a Local User has limited permissions.

This prevents accidental modifications to critical system files.

In production environments, administrators usually log in as a Local User and use administrative privileges only when required.

This improves the overall security of the operating system.

---

# Root User vs Local User

| Root User                       | Local User                                                         |
| ------------------------------- | ------------------------------------------------------------------ |
| Administrator account           | Regular user account                                               |
| Full system access              | Limited access                                                     |
| Can modify system configuration | Cannot modify critical system settings without elevated privileges |
| Can install or remove software  | Requires administrative permission for system changes              |
| Used for system administration  | Used for everyday work                                             |

---

# Best Practices

While working with Linux virtual machines, follow these practices:

* Keep the Root Password secure.
* Use a Local User for daily activities.
* Allocate only the required CPU and RAM.
* Keep enough free storage inside the VM.
* Shut down the virtual machine properly before closing VMware.
* Take snapshots before making major configuration changes.

---



---

# Important Questions

### 1. What is Virtualization?

Virtualization is the technology of creating multiple virtual machines on a single physical computer.

---

### 2. What is a Virtual Machine?

A Virtual Machine is a software-based computer that behaves like a physical computer and runs its own operating system.

---

### 3. What is a Hypervisor?

A Hypervisor is software that creates, runs, and manages virtual machines.

---

### 4. What is the difference between Type-1 and Type-2 Hypervisors?

| Type-1 Hypervisor               | Type-2 Hypervisor                         |
| ------------------------------- | ----------------------------------------- |
| Installed directly on hardware  | Installed on an existing operating system |
| Better performance              | Slightly lower performance                |
| Used in production data centers | Used for learning and testing             |
| Example: VMware ESXi            | Example: VMware Workstation               |

---

### 5. Why do we use VMware Workstation?

VMware Workstation allows us to install multiple operating systems as virtual machines without modifying the host operating system.

---

### 6. Why is the Root User important?

The Root User has complete administrative control over the Linux operating system and is responsible for managing the entire system.

---



