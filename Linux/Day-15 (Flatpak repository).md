# Flatpak Repository and Application Management

## 1. What is Flatpak?

**Flatpak** is a technology used to install and run applications on Linux.

It is mainly popular for **desktop/GUI applications**.

For example:

* GIMP
* VSCodium
* VLC
* LibreOffice
* Firefox
* Many other GUI applications

Flatpak applications are usually distributed through a **Flatpak repository**, also called a **remote**.

The most popular Flatpak repository is:

**Flathub**

---

# 2. Flatpak vs YUM/DNF

First, one important correction:

> **Flatpak does NOT replace YUM or DNF.**

YUM/DNF and Flatpak solve related but different problems.

### YUM/DNF

YUM/DNF are Linux **package managers** used mainly for installing and managing RPM packages.

Example:

```bash
dnf install httpd
```

This installs the Apache HTTP Server package.

YUM/DNF are commonly used for:

* System packages
* Libraries
* Server software
* Services
* Development packages
* Dependencies required by the operating system

### Flatpak

Flatpak is mainly used for **desktop applications**, especially when we want applications to be packaged with their required runtime/dependencies and run in a more isolated environment.

Example:

```bash
flatpak install flathub org.gimp.GIMP
```

---

# 3. Simple Comparison

Think about it like this:

```text
YUM / DNF
     |
     +---- RPM packages
     |
     +---- System software
     |
     +---- Server packages
     |
     +---- Libraries
     |
     +---- Dependencies


Flatpak
     |
     +---- Desktop applications
     |
     +---- Application runtime
     |
     +---- Sandboxed application
     |
     +---- Can be installed per-user
```

So:

> **YUM/DNF = traditional Linux package management**

> **Flatpak = application distribution and sandboxing, especially useful for desktop applications**

---

# 4. What is a Flatpak Repository?

A **Flatpak repository** is a location from which Flatpak applications and their required components can be downloaded.

The most popular repository is:

```text
Flathub
```

Flathub contains a very large collection of Linux desktop applications.

For example:

```text
GIMP
VLC
VSCodium
LibreOffice
Firefox
```

The official Flathub repository can be added using its `.flatpakrepo` file. Flatpak documentation describes a `.flatpakrepo` file as containing repository information and its GPG key.

Example:

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

The name `flathub` is simply the **local name** we give to that remote repository.

---

# 5. Is Flathub Similar to a YUM Repository?

Yes, conceptually they are similar.

With YUM/DNF:

```text
YUM/DNF
   |
   v
Repository
   |
   v
RPM Packages
```

With Flatpak:

```text
Flatpak
   |
   v
Remote Repository
   |
   v
Flatpak Applications
```

But internally they work differently.

Do not say:

> "Flatpak repository replaces the YUM repository."

Instead say:

> "Flatpak provides another application distribution mechanism, mainly useful for desktop applications."

---

# 6. YUM Repository vs Flatpak Remote

In traditional YUM/DNF configuration, we commonly work with repository configuration files under:

```text
/etc/yum.repos.d/
```

For example:

```text
/etc/yum.repos.d/myrepo.repo
```

Inside the file we may define:

```text
[myrepo]
name=My Repository
baseurl=http://example.com/repo/
enabled=1
gpgcheck=1
```

Then DNF/YUM can use that repository.

With Flatpak, we can add a remote using:

```bash
flatpak remote-add
```

Example:

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

So the basic idea is similar:

```text
Repository configured
        ↓
Application/package searched
        ↓
Application/package installed
```

---

# 7. How Does Flatpak Work?

Suppose we want to install GIMP.

The basic flow is:

```text
Flathub
   |
   | Search
   v
GIMP
   |
   | Install
   v
Flatpak
   |
   | Runtime + required components
   v
GIMP Application
```

Flatpak applications can use a shared **runtime**, which provides common libraries and components needed by applications.

This means applications do not necessarily need to depend directly on the host operating system's versions of all those libraries.

---

# 8. Important Concept: System-wide vs Per-user

This is one of the most important concepts for the Linux administrator.

Flatpak supports two installation scopes:

### System-wide

Application is installed for all users.

```text
System
 |
 +--- User A → Can use application
 |
 +--- User B → Can use application
 |
 +--- User C → Can use application
```

### Per-user

Application is installed only for one particular user.

```text
System
 |
 +--- User A → Application installed
 |
 +--- User B → Cannot use that user installation
 |
 +--- User C → Cannot use that user installation
```

Flatpak officially supports both **system-wide** and **per-user** installations. A per-user repository is also available only to that particular user.

---

# 9. How to Install Flatpak

Before using Flatpak, the Flatpak package itself must be installed.

On many RHEL/Fedora-based systems, we can use:

```bash
dnf install flatpak -y
```

Verify:

```bash
flatpak --version
```

Example output:

```text
Flatpak 1.x.x
```

If the package is not available, first make sure the required OS repositories are configured and accessible.

---

# 10. Add Flathub Repository System-wide

To add Flathub for the whole system:

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

Now check the configured remotes:

```bash
flatpak remotes
```

You may see:

```text
Name
flathub
```

This means the Flathub remote is configured.

---

# 11. Search for an Application

Use:

```bash
flatpak search gimp
```

Or:

```bash
flatpak search codium
```

The search normally displays the application name and its **Application ID**.

For example, the current Flathub application ID for VSCodium is:

```text
com.vscodium.codium
```

This Application ID is important because Flatpak commands commonly use the ID to identify the application.

---

# 12. Install an Application

For a normal system-wide installation:

```bash
flatpak install flathub org.gimp.GIMP
```

Flatpak will ask for confirmation if required.

To automatically answer yes:

```bash
flatpak install flathub org.gimp.GIMP -y
```

---

# 13. Run an Application

After installation:

```bash
flatpak run org.gimp.GIMP
```

The basic format is:

```bash
flatpak run APPLICATION_ID
```

Example:

```bash
flatpak run com.vscodium.codium
```

---

# 14. Check Installed Applications

Use:

```bash
flatpak list
```

This displays installed Flatpak applications and runtimes.

You can also specifically list applications:

```bash
flatpak list --app
```

Flatpak's `list` command can show system-wide and per-user installations, while `--user` limits the operation to the per-user installation.

---

# 15. Per-user Flatpak Installation

Now we come to the most important practical part.

Suppose we have a Linux user:

```text
bammbamm
```

The requirement is:

> Install a Flatpak application only for user `bammbamm`.

We need to use:

```bash
--user
```

The `--user` option tells Flatpak:

> "Perform this operation for the current user only."

---

# 16. Configure a Per-user Repository

First switch to the user:

```bash
su - bammbamm
```

Now add the repository:

```bash
flatpak remote-add --user --if-not-exists extra https://dl.flathub.org/repo/flathub.flatpakrepo
```

Notice the important option:

```text
--user
```

And notice that we named the repository:

```text
extra
```

So:

```text
extra = local name of the Flatpak remote
```

It does not mean that `extra` is a special Flatpak repository.

We could have named it:

```text
myrepo
```

or:

```text
flathub-user
```

The name is chosen by us.

---

# 17. Verify the Per-user Repository

Run:

```bash
flatpak remotes --user
```

You should see:

```text
extra
```

This tells us that the remote belongs to the current user.

We can also use:

```bash
flatpak remotes --user --show-details
```

---

# 18. Search for Codium

Now search:

```bash
flatpak search --user codium
```

You may see VSCodium.

Its Application ID is:

```text
com.vscodium.codium
```

The current Flathub listing identifies VSCodium with this Application ID.

---

# 19. Install Codium for Only bammbamm

Use:

```bash
flatpak install --user extra com.vscodium.codium -y
```

Here:

```text
flatpak
    ↓
install
    ↓
--user
    ↓
extra
    ↓
com.vscodium.codium
```

Meaning:

* `flatpak` → Flatpak command
* `install` → install application
* `--user` → install only for current user
* `extra` → repository/remote name
* `com.vscodium.codium` → application ID
* `-y` → automatically answer yes

---

# 20. Verify the Application

Run:

```bash
flatpak list --user
```

You should see the installed application.

You can also check:

```bash
flatpak info --user com.vscodium.codium
```

This gives information about the application.

---

# 21. Run Codium

Run:

```bash
flatpak run com.vscodium.codium
```

Because the application was installed for `bammbamm`, this installation belongs to that user.

---

# 22. Verify Other Users Cannot See the User Installation

Now exit from `bammbamm`:

```bash
exit
```

Or:

```bash
Ctrl + D
```

You are now back to the previous user.

Run:

```bash
flatpak list --user
```

The `bammbamm` user's application will not appear in the current user's per-user installation.

This demonstrates the difference between:

```text
System-wide installation
```

and:

```text
Per-user installation
```

---

# 23. Complete Practical Assignment

## Assignment

**Configure a Flatpak remote repository named `extra` that is available only to the user `bammbamm`.**

Repository URL:

```text
https://dl.flathub.org/repo/flathub.flatpakrepo
```

Using the `extra` repository, install the Flatpak application **Codium/VSCodium** for the user `bammbamm`.

Finally, verify that the repository and application are accessible only to `bammbamm` and not as a per-user installation for other users.

---

# 24. Assignment Solution

### Step 1: Switch to bammbamm

```bash
su - bammbamm
```

### Step 2: Add the repository for this user

```bash
flatpak remote-add --user --if-not-exists extra https://dl.flathub.org/repo/flathub.flatpakrepo
```

### Step 3: Verify the repository

```bash
flatpak remotes --user
```

Expected:

```text
extra
```

### Step 4: Search for Codium

```bash
flatpak search --user codium
```

Find the VSCodium Application ID:

```text
com.vscodium.codium
```

### Step 5: Install the application

```bash
flatpak install --user extra com.vscodium.codium -y
```

### Step 6: Verify installation

```bash
flatpak list --user
```

### Step 7: Check application information

```bash
flatpak info --user com.vscodium.codium
```

### Step 8: Exit from bammbamm

```bash
exit
```

Or:

```bash
Ctrl + D
```

### Step 9: Check from another user

```bash
flatpak remotes --user
```

The `extra` remote configured for `bammbamm` should not appear in the current user's per-user remotes.

Also:

```bash
flatpak list --user
```

The VSCodium installation belonging to `bammbamm` should not appear as the current user's per-user installation.

---

# 25. Very Important: Don't Confuse `--user` and System-wide

Remember this simple rule:

```text
Without --user
        ↓
System-wide operation
```

```text
With --user
        ↓
Current user's operation
```

Example:

### System-wide repository

```bash
flatpak remote-add flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

### Per-user repository

```bash
flatpak remote-add --user extra https://dl.flathub.org/repo/flathub.flatpakrepo
```

---

# 26. System-wide Application Installation

```bash
flatpak install flathub org.gimp.GIMP
```

Conceptually:

```text
System
  |
  +--- User A → GIMP available
  |
  +--- User B → GIMP available
  |
  +--- User C → GIMP available
```

---

# 27. Per-user Application Installation

```bash
flatpak install --user extra com.vscodium.codium
```

Conceptually:

```text
System
  |
  +--- bammbamm → VSCodium available
  |
  +--- User B   → Not installed for this user
  |
  +--- User C   → Not installed for this user
```

---

# 28. Most Important Commands

| Purpose                 | Command                                |
| ----------------------- | -------------------------------------- |
| Check Flatpak           | `flatpak --version`                    |
| Add remote              | `flatpak remote-add`                   |
| List remotes            | `flatpak remotes`                      |
| List user remotes       | `flatpak remotes --user`               |
| Search application      | `flatpak search APP`                   |
| Install application     | `flatpak install REMOTE APP_ID`        |
| User installation       | `flatpak install --user REMOTE APP_ID` |
| List applications       | `flatpak list`                         |
| List user applications  | `flatpak list --user`                  |
| Application information | `flatpak info APP_ID`                  |
| Run application         | `flatpak run APP_ID`                   |
| Update applications     | `flatpak update`                       |
| Remove application      | `flatpak uninstall APP_ID`             |

---

# 29. Common Mistakes

### Mistake 1: Writing `flatpack`

Wrong:

```bash
flatpack
```

Correct:

```bash
flatpak
```

---

### Mistake 2: Using a normal hyphen instead of `--user`

Correct:

```bash
flatpak install --user extra com.vscodium.codium
```

`--user` contains **two normal hyphens**.

---

### Mistake 3: Using `codium` as the application ID without checking

First search:

```bash
flatpak search codium
```

Then use the correct Application ID.

For current VSCodium on Flathub:

```text
com.vscodium.codium
```

---

### Mistake 4: Forgetting `--user`

If the requirement says:

> "Install only for bammbamm"

then remember:

```bash
--user
```

For example:

```bash
flatpak install --user extra com.vscodium.codium
```

---

# 30. Easy Way to Remember the Practical

For a **per-user Flatpak assignment**, remember this sequence:

```text
1. Switch user
      ↓
2. Add repository with --user
      ↓
3. Check repository with --user
      ↓
4. Search application
      ↓
5. Install with --user
      ↓
6. Check application with --user
      ↓
7. Exit user
      ↓
8. Verify it is not installed for another user
```

Commands:

```bash
su - bammbamm

flatpak remote-add --user --if-not-exists extra https://dl.flathub.org/repo/flathub.flatpakrepo

flatpak remotes --user

flatpak search --user codium

flatpak install --user extra com.vscodium.codium -y

flatpak list --user

exit

flatpak remotes --user

flatpak list --user
```

---

# 31. One Important Correction to the Original Understanding

It is tempting to remember it like this:

```text
YUM/DNF → Server applications
Flatpak → GUI applications
```

This is a **useful beginner-level rule**, but it is not a strict technical rule.

For example, some GUI applications can be installed through RPM/DNF, and Flatpak is not limited to only GUI software.

A better statement is:

> **DNF manages traditional RPM packages provided by Linux repositories, while Flatpak provides an application distribution and sandboxing model that is especially common for desktop applications.**

Also, Flatpak's per-user capability is **not the same thing as saying the application is completely isolated from the operating system**. Flatpak applications run in a sandbox with controlled access to host resources, and permissions can be managed.

---

# 32. Final Classroom Summary

If a student asks:

### "What is Flatpak?"

Answer:

> Flatpak is a Linux technology used to distribute, install, and run applications, especially desktop applications, in a sandboxed environment.

### "What is Flathub?"

Answer:

> Flathub is a popular Flatpak application repository from which we can find and install many Linux applications.

### "Does Flatpak replace DNF?"

Answer:

> No. DNF and Flatpak are different tools. DNF manages RPM packages, while Flatpak provides another application distribution and sandboxing mechanism.

### "Can Flatpak install an application only for one user?"

Answer:

> Yes. Use the `--user` option.

Example:

```bash
flatpak install --user extra com.vscodium.codium
```

### "What is the most important thing in the assignment?"

Remember:

```text
--user
```

It tells Flatpak to work with the **current user's installation** rather than the default system-wide installation.

### One-line memory trick

```text
YUM/DNF → RPM packages
Flatpak → Applications
Flathub → Flatpak repository
--user → Current user only
```
