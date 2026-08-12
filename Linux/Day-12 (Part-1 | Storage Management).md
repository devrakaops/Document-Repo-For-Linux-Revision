# Day-12 Part-1 |  Linux Storage Management

## Introduction

Storage is one of the most important parts of a Linux system because applications and servers continuously generate and store data.

Whether we are working with databases, application files, logs, backups, or user data, all of this information ultimately needs storage.

In Linux administration, we should understand how to:

* Add a new disk
* Create partitions
* Select a partitioning scheme
* Create a file system
* Mount storage
* Verify storage
* Unmount storage
* Remove a partition safely
* Configure permanent mounting using `/etc/fstab`

The storage management session covered these concepts step by step.

---

# Storage Evolution

Storage technology has changed significantly over time.

We have used different types of storage media such as:

```text
CD
 ↓
Floppy Disk
 ↓
USB Pen Drive
 ↓
HDD
 ↓
SSD
 ↓
Cloud / Virtual Storage
```

### CD

Compact Discs were commonly used for distributing software, movies, and other static data.

### Floppy Disk

Floppy disks were older storage devices with very small capacities, generally measured in MB.

### USB Pen Drive

USB drives provided much more storage than floppy disks.

Earlier USB drives commonly had capacities such as:

```text
4 GB
8 GB
```

Modern USB drives are available in much larger capacities such as:

```text
64 GB
128 GB
and more
```

### HDD and SSD

Modern servers commonly use:

* HDD — Hard Disk Drive
* SSD — Solid State Drive

These provide large amounts of local storage.

### Cloud and Virtual Storage

In cloud environments, storage can be provided virtually.

For example, a cloud administrator can attach additional storage to a virtual machine and then manage it inside Linux just like other block devices.

---

# Basic Storage Deployment Workflow

Whenever we add a new disk to a Linux system, we generally follow this sequence:

```text
Disk Attachment
      ↓
Partitioning
      ↓
File System Creation
      ↓
Mounting
```

These four steps are very important.

### Disk Attachment

First, we add or attach the physical or virtual disk to the system.

For a virtual machine, this can be done from the hypervisor such as VMware or VirtualBox.

### Partitioning

After the disk is visible to Linux, we divide the disk into partitions.

### File System Creation

A partition is not immediately ready to store normal Linux files.

We need to create a file system on it.

For example:

```bash
mkfs.ext4 /dev/nvme0n2p1
```

### Mounting

Finally, we connect the file system to a directory.

For example:

```bash
mkdir /device
mount /dev/nvme0n2p1 /device
```

After mounting, we can use `/device` to access the storage.

---

# Understanding Disk Partitioning

Partitioning creates logical divisions on a disk.

For example:

```text
Disk
 |
 +---- Partition 1
 |
 +---- Partition 2
 |
 +---- Partition 3
```

The disk is not physically cut into separate pieces.

Instead, Linux understands these areas as separate logical partitions.

For example, one partition can be used for application data while another can be used for other purposes.

This provides isolation between different types of data.

If one partition becomes full, it does not automatically consume the space belonging to another partition.

Partitioning can also be useful for:

* Separating system and user data
* Data recovery
* Dual-boot configurations
* Isolating application or virtual-machine data

---

# MBR Partitioning Scheme

MBR stands for:

**Master Boot Record**

It is an older partitioning scheme commonly associated with traditional BIOS systems.

The traditional MBR structure contains:

```text
Boot Code       → 446 bytes
Partition Table → 64 bytes
Boot Signature  → 2 bytes

Total           → 512 bytes
```

MBR traditionally supports:

```text
Maximum 4 primary partitions
```

An extended partition can be used to create logical partitions.

The session discussed the traditional arrangement of:

```text
3 Primary Partitions
        +
1 Extended Partition
        ↓
Logical Partitions
```

The extended partition acts as a container for logical partitions.

MBR is generally associated with older BIOS-based systems.

---

# GPT Partitioning Scheme

GPT stands for:

**GUID Partition Table**

GPT is the modern partitioning scheme and is commonly used with UEFI systems.

It supports many more partitions than traditional MBR.

The session covered:

```text
MBR → Older partitioning approach
GPT → Modern partitioning approach
```

GPT also maintains backup partition information at the end of the disk, which provides additional protection for partition metadata.

GPT is therefore preferred for modern systems and large-capacity storage.

---

# MBR vs GPT

| Feature                     | MBR                   | GPT                      |
| --------------------------- | --------------------- | ------------------------ |
| Common firmware             | BIOS                  | UEFI                     |
| Type                        | Traditional           | Modern                   |
| Partition support           | Limited               | Many partitions          |
| Extended/logical partitions | Supported             | Not required             |
| Partition metadata          | Traditional structure | More robust metadata     |
| Large disks                 | Limited               | Designed for large disks |

The important thing to remember is:

```text
MBR → Traditional
GPT → Modern
```

---

# Linux File System

After creating a partition, we still cannot directly use it like a normal Linux directory.

We need a file system.

A file system provides the structure Linux uses to:

* Store files
* Store directories
* Track files
* Read data
* Write data
* Manage available storage blocks

A simple way to understand the process is:

```text
Disk
 ↓
Partition
 ↓
File System
 ↓
Mount Point
 ↓
Files and Directories
```

---

# Common Linux File Systems

## ext2

`ext2` is an older Linux file system.

One important characteristic is:

> ext2 does not provide journaling.

Because there is no journal, recovery after an unexpected shutdown can be more difficult.

---

## ext3

`ext3` introduced journaling.

A journal keeps track of file system operations so that the system can recover more easily after an unexpected shutdown.

The basic idea is:

```text
File System Operation
        ↓
Journal
        ↓
Actual File System Change
```

This makes recovery more reliable than a non-journaling file system.

---

## ext4

`ext4` is a widely used Linux file system.

It provides:

* Journaling
* Large file and file-system support
* Good performance
* File-system integrity features

It is commonly used in Linux environments.

---

## XFS

XFS is another important Linux file system, especially in enterprise environments.

It is designed to work efficiently with large amounts of data and large file systems.

XFS is commonly encountered on enterprise Linux systems.

---

## VFAT / FAT32

VFAT/FAT32 is useful when storage needs to be shared between different operating systems.

It is commonly supported by:

```text
Windows
Linux
macOS
```

One important limitation of FAT32 is:

```text
Maximum single file size ≈ 4 GB
```

Therefore, a file larger than 4 GB cannot normally be stored on a FAT32 file system.

---

# Adding a New Disk

Suppose we want to add a new 5 GB disk to a Linux virtual machine.

First, we attach the disk from the virtualization platform.

For example:

```text
VirtualBox / VMware
        ↓
Virtual Machine Settings
        ↓
Hard Disk
        ↓
Add New Disk
        ↓
Select Disk Size
        ↓
Save
```

After adding the disk, boot the Linux machine.

Now we need to check whether Linux can see the disk.

---

# Checking Block Devices with `lsblk`

Use:

```bash
lsblk
```

`lsblk` means:

**List Block Devices**

It displays disks, partitions, and their relationships.

For example:

```text
NAME        SIZE
nvme0n1     20G
├─nvme0n1p1  1G
└─nvme0n1p2 19G

nvme0n2      5G
```

Here:

```text
nvme0n1 → Existing disk
nvme0n2 → Newly attached disk
```

The exact device name depends on the system.

It may appear as:

```text
/dev/sdb
/dev/nvme0n2
```

---

# Creating a Partition with `fdisk`

The `fdisk` command is used for disk partition management.

For example:

```bash
fdisk /dev/nvme0n2
```

This opens an interactive partitioning interface.

Inside `fdisk`, some important commands are:

| Command | Purpose                      |
| ------- | ---------------------------- |
| `m`     | Display help                 |
| `p`     | Print partition table        |
| `n`     | Create a new partition       |
| `d`     | Delete a partition           |
| `g`     | Create a GPT partition table |
| `w`     | Write changes and exit       |
| `q`     | Quit without saving          |

---

# Creating a 1 GB Partition

Suppose the new disk is:

```text
/dev/nvme0n2
```

Run:

```bash
fdisk /dev/nvme0n2
```

Inside `fdisk`:

```text
n
```

This creates a new partition.

Then select:

```text
p
```

for a primary partition.

Press `Enter` to accept the default partition number.

Press `Enter` to accept the default starting sector.

For the partition size, enter:

```text
+1G
```

Then use:

```text
p
```

to verify the partition table.

Finally:

```text
w
```

writes the changes to the disk.

The resulting partition may look like:

```text
/dev/nvme0n2p1
```

---

# Important Difference Between `w` and `q`

This is very important while working with `fdisk`.

### `w`

```text
w
```

writes the partition changes to disk and exits.

### `q`

```text
q
```

exits without saving the pending changes.

So remember:

```text
w → Save
q → Quit without saving
```

---

# Creating a File System

After creating the partition, we need to create a file system.

For example, to create an `ext4` file system:

```bash
mkfs.ext4 /dev/nvme0n2p1
```

`mkfs` means:

**Make File System**

The command creates the required file-system structures on the partition.

After this step, the partition is formatted as `ext4`.

---

# Mounting the Partition

Creating a file system is not enough.

We need to mount it to a directory so that users and applications can access it.

First create a mount point:

```bash
mkdir /device
```

Then mount the partition:

```bash
mount /dev/nvme0n2p1 /device
```

Now the file system is accessible through:

```text
/device
```

The complete flow is:

```text
/dev/nvme0n2
      ↓
Partition
      ↓
/dev/nvme0n2p1
      ↓
ext4 file system
      ↓
/device
      ↓
Files and Directories
```

---

# Verifying the Mount

Run:

```bash
lsblk
```

You should be able to see the mount point associated with the partition.

We can now enter the directory:

```bash
cd /device
```

For practice, the session created directories and files using:

```bash
mkdir DIR{1..50}
```

and:

```bash
touch file{1..50}
```

This creates:

```text
DIR1
DIR2
DIR3
...
DIR50
```

and:

```text
file1
file2
file3
...
file50
```

---

# Unmounting Storage

To unmount the storage:

```bash
umount /device
```

Unmounting does **not** delete the files.

It only removes the connection between the file system and the directory.

For example:

```text
/dev/nvme0n2p1
        ↓
     ext4
        ↓
     /device
```

After:

```bash
umount /device
```

the connection is removed.

The data remains on the partition.

---

# Understanding the "Target is Busy" Error

Suppose we are currently inside:

```bash
cd /device
```

and then try:

```bash
umount /device
```

Linux may report that the target is busy.

The reason is simple:

> Our current shell is using the directory that we are trying to unmount.

Move somewhere else first:

```bash
cd ..
```

Then run:

```bash
umount /device
```

---

# Classroom Door Analogy for Mount and Unmount

Think of `/device` as the door of a classroom.

Inside the classroom are:

```text
DIR1
DIR2
...
DIR50

file1
file2
...
file50
```

When we unmount:

```bash
umount /device
```

we are closing the door.

We are **not deleting the classroom or its contents**.

We can create another directory:

```bash
mkdir /new
```

and mount the same partition there:

```bash
mount /dev/nvme0n2p1 /new
```

Now:

```bash
cd /new
```

The previously created files and directories will still be available.

This demonstrates an important concept:

> Mounting controls where we access the file system. Unmounting does not delete the data.

---

# Checking Disk Usage with `df`

The `df` command shows file-system disk usage.

Use:

```bash
df -h
```

The `-h` option means human-readable output.

Instead of seeing only raw numbers, we can see values such as:

```text
1G
5G
20G
```

---

# Checking File System Type with `df -hT`

Use:

```bash
df -hT
```

The `-T` option displays the file-system type.

For example:

```text
Filesystem     Type   Size  Used Avail
/dev/sdb1      ext4   1G
```

This allows us to see both:

```text
Disk Usage
+
File System Type
```

---

# Checking UUID with `blkid`

Use:

```bash
blkid
```

This command displays information such as:

* Device
* UUID
* File-system type

Example:

```text
/dev/nvme0n2p1: UUID="abc-123-xyz" TYPE="ext4"
```

The UUID uniquely identifies the file system.

UUID is especially important when configuring permanent mounts.

---

# Removing a Partition

When we want to completely remove a partition, we should reverse the storage creation process carefully.

The session followed this order:

```text
Unmount
   ↓
Wipe File System Signature
   ↓
Delete Partition
```

---

# Unmount the Partition

First:

```bash
umount /mount_point
```

For example:

```bash
umount /device
```

---

# Remove the File System Signature with `wipefs`

Run:

```bash
wipefs /dev/nvme0n2p1
```

This removes the file-system signature information from the partition.

The purpose is to clean the existing file-system metadata before removing the partition structure.

---

# Delete the Partition

Open the disk with:

```bash
fdisk /dev/nvme0n2
```

Then:

```text
d
```

Select the partition that needs to be deleted.

Finally:

```text
w
```

to write the changes.

The overall removal process is:

```text
umount
   ↓
wipefs
   ↓
fdisk → d
   ↓
fdisk → w
```

Always be extremely careful when using these commands because storage deletion can permanently destroy data.

---

# Temporary Mount vs Permanent Mount

When we run:

```bash
mount /dev/nvme0n2p1 /device
```

the mount is temporary.

If the server is rebooted, the mount configuration does not automatically remain.

This creates a problem for servers.

For example:

```text
Server
  ↓
Reboot
  ↓
/device may no longer be mounted
```

To automatically mount the partition during boot, Linux provides:

```text
/etc/fstab
```

---

# `/etc/fstab`

`/etc/fstab` contains file-system mount configuration.

Linux reads this configuration during boot and uses it to mount the required file systems.

A typical entry contains six fields:

```text
[Device] [Mount Point] [File System] [Options] [Dump] [FSCK]
```

Example:

```text
UUID="abc-123-xyz" /new ext4 defaults 0 0
```

---

# Understanding the `/etc/fstab` Fields

## Device / UUID

Example:

```text
UUID="abc-123-xyz"
```

This identifies the file system.

Using UUID is preferred because device names such as:

```text
/dev/sdb
/dev/sdc
```

can change depending on disk detection and configuration.

---

## Mount Point

Example:

```text
/new
```

This is the directory where the file system should be mounted.

---

## File System Type

Example:

```text
ext4
```

This should match the file system created on the partition.

---

## Mount Options

Example:

```text
defaults
```

The `defaults` option provides the standard default mount behavior.

---

## Dump

Example:

```text
0
```

This field is related to the traditional dump backup mechanism.

---

## FSCK

Example:

```text
0
```

This field controls file-system checking behavior during boot.

---

# Example `/etc/fstab` Entry

```text
UUID="abc-123-xyz" /new ext4 defaults 0 0
```

The structure is:

```text
UUID                  Mount Point   Type   Options   Dump   FSCK
 │                         │          │       │         │      │
 ↓                         ↓          ↓       ↓         ↓      ↓
UUID="abc-123-xyz"       /new       ext4   defaults    0      0
```

---

# Very Important: Test `/etc/fstab`

One of the most important rules when modifying `/etc/fstab` is:

> Never reboot immediately after editing `/etc/fstab` without testing the configuration.

A typing mistake in `/etc/fstab` can cause mounting problems during boot and may result in the system entering an emergency or rescue environment.

First test the configuration with:

```bash
mount -a
```

If there is no error, the configuration can be considered valid for the tested mounts.

After that, reboot and verify the mount again.

For example:

```bash
init 6
```

After logging back in:

```bash
lsblk
```

Verify that the partition is mounted automatically.

---

# Complete Practical Flow

The complete storage management workflow can be remembered as:

```text
Attach Disk
     ↓
lsblk
     ↓
fdisk
     ↓
Create Partition
     ↓
mkfs.ext4
     ↓
Create Mount Point
     ↓
mount
     ↓
Verify with lsblk / df / blkid
     ↓
Use Storage
     ↓
umount
```

For permanent mounting:

```text
Create File System
     ↓
Find UUID using blkid
     ↓
Create Mount Point
     ↓
Configure /etc/fstab
     ↓
Run mount -a
     ↓
Check for Errors
     ↓
Reboot
     ↓
Verify with lsblk
```

---

# Important Commands for Revision

### List block devices

```bash
lsblk
```

### Open a disk with fdisk

```bash
fdisk /dev/nvme0n2
```

### Create ext4 file system

```bash
mkfs.ext4 /dev/nvme0n2p1
```

### Create mount point

```bash
mkdir /device
```

### Mount partition

```bash
mount /dev/nvme0n2p1 /device
```

### Unmount partition

```bash
umount /device
```

### Check disk usage

```bash
df -h
```

### Check disk usage and file-system type

```bash
df -hT
```

### Check UUID and file-system type

```bash
blkid
```

### Remove file-system signature

```bash
wipefs /dev/nvme0n2p1
```

### Test `/etc/fstab`

```bash
mount -a
```

### Reboot

```bash
init 6
```

---

# Final Revision

The most important storage concept is:

```text
Disk
 ↓
Partition
 ↓
File System
 ↓
Mount Point
 ↓
Files / Directories
```

The practical sequence is:

```text
Attach Disk
    ↓
lsblk
    ↓
fdisk
    ↓
Partition
    ↓
mkfs
    ↓
Mount
    ↓
Verify
    ↓
Use Storage
```

For permanent mounting:

```text
blkid
    ↓
UUID
    ↓
/etc/fstab
    ↓
mount -a
    ↓
Reboot
    ↓
Verify
```

And when removing storage:

```text
Unmount
    ↓
wipefs
    ↓
Delete Partition
```

The complete session is therefore centered around one simple idea:

> **Linux storage management means taking a disk, preparing it, creating a file system, making it available through a mount point, verifying it, and configuring it properly for permanent use when required.**
