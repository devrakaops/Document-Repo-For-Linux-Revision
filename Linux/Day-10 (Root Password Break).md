# Day-10 Linux Root Password Reset / Password Break

## Introduction

In Linux, if the **root password is forgotten**, we can recover access by using the **GRUB boot menu**.

This process is commonly called **Linux Password Break** or **Root Password Reset**.

The basic idea is:

> We temporarily modify the Linux boot parameters from GRUB, enter a special maintenance environment, reset the root password, apply SELinux relabeling, and then reboot the system normally.

This method is useful for system administrators when they have **physical or console access to the server**.

---

## When Do We Need Password Break?

Suppose a Linux server is working normally, but the administrator has forgotten the `root` password.

Normally:

```text
Username: root
Password: ********
```

Without the correct password, we cannot log in as root.

Instead of reinstalling the operating system, we can use the **GRUB boot process** to get temporary administrative access and reset the password.

The important point is that this process does **not** recover the old password.

It allows us to **set a new password**.

---

# Basic Password Break Flow - step by step

---

# Step 1: Restart the System

First, restart the Linux machine.

For example:

```bash
reboot
```

or restart the machine from the console.

During the boot process, the **GRUB menu** will appear.

---

# Step 2: Open the GRUB Edit Screen - "rescue" kernel image

When the GRUB menu appears, select the Linux operating system kernel image entry that has "rescue" kernel line.

Do **not** press Enter immediately.

Press:

```text
e
```

The `e` key allows us to **edit the selected GRUB boot entry temporarily**.

You will see a screen containing several boot parameters.

---

# Step 3: Find the Linux Kernel Line

Look for the line that starts with:

```text
linux
```

or, depending on the Linux distribution/kernel configuration:

```text
linux16
```

This line contains the parameters that are passed to the Linux kernel during boot.

---

# Step 4: Go to the End of the Linux Line

Move the cursor to the **end of the line**.

Add:

```text
rw init=/bin/bash
```

So the end of the line will look similar to:

```text
... rw init=/bin/bash
```

### What does this mean?

`rw` means:

```text
Read-Write
```

It tells the system that the filesystem should be mounted with write access.

`init=/bin/bash` tells the kernel to start a Bash shell instead of starting the normal Linux initialization process.

So instead of getting the normal login screen, we get a shell with administrative access.

---

# Step 5: Boot Using the Modified Parameters

After modifying the line, press:

```text
Ctrl + X
```

This starts the system using the temporarily modified boot parameters.

The system will boot into a shell instead of the normal login process.

You may see a prompt similar to:

```text
bash-5.1#
```

or:

```text
sh-5.1#
```

At this point, we have access to the system at a very early stage of boot.

---

# Step 6: Change the Root Password

Now execute:

```bash
passwd root
```

The system will ask for the new password.

Example:

```text
bash-5.1# passwd root
Changing password for user root.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

The old password is not required.

We are setting a **new root password**.

---

# Step 7: SELinux Relabeling

On systems where **SELinux** is enabled, we should create the file:

```bash
touch /.autorelabel
```

This tells SELinux:

> On the next boot, relabel the filesystem according to the correct SELinux contexts.

Run:

```bash
touch /.autorelabel
```

This step is important because changing the system from the special password-break environment can otherwise cause SELinux context-related problems.

---

# Step 8: Reboot the System

After resetting the password and creating the relabel file, reboot the machine.

For example:

```bash
/sbin/reboot -f
```

The `-f` option forces the reboot.

Depending on the environment, another reboot method may also be required.

The system will now boot normally.

---

# Step 9: Login Using the New Password

After the normal boot process finishes, the regular login prompt will appear.

Use:

```text
Username: root
Password: <new-password>
```

The new password should now work.

---

# Complete Command Sequence

For quick revision, the important commands are:

```bash
passwd root
```

Then:

```bash
touch /.autorelabel
```

Then reboot:

```bash
/sbin/reboot -f
```

The important GRUB modification is:

```text
rw init=/bin/bash
```

So the complete practical flow is:

```text
GRUB
  ↓
Press e
  ↓
Find linux line
  ↓
Add:
rw init=/bin/bash
  ↓
Ctrl + X
  ↓
passwd root
  ↓
touch /.autorelabel
  ↓
/sbin/reboot -f
  ↓
Login with new root password
```

---
