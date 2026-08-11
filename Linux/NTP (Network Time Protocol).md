# Day 10 — NTP, TAR & Linux Performance Tuning

## 1. Network Time Protocol (NTP)

### 1.1 Why Time Synchronization Is Important

In Linux administration, maintaining the correct system time is very important.

Imagine that we have two servers:

```text
Server 1 → India
Server 2 → USA
```

Suppose Server 1 shows:

```text
10:00 AM
```

while Server 2 shows:

```text
10:30 AM
```

Now imagine that both servers are communicating with each other.

A transaction may be recorded as:

```text
Server 1: Transaction started at 10:05
Server 2: Transaction received at 09:40
```

This creates confusion.

Time synchronization becomes especially important in:

* Database replication
* Distributed applications
* Server-to-server communication
* Log analysis
* Packet/event tracking
* Security investigation
* Troubleshooting
* Monitoring systems
* Cluster environments

### Example: Security Investigation

Suppose someone attacks a server.

The security team checks logs from multiple servers:

```text
Web Server
Application Server
Database Server
Firewall
Load Balancer
```

If every server has a different time, it becomes difficult to determine:

```text
Which event happened first?
When did the attack start?
Which server was compromised first?
```

Therefore:

> **All systems in a distributed environment should maintain synchronized time.**

---

# 2. Timezone vs. Actual System Time

Before configuring NTP, understand an important difference.

### Timezone

Timezone determines **how the system displays the time**.

For example:

```text
Asia/Kolkata
America/New_York
America/Los_Angeles
Europe/London
```

### System Clock

The system clock represents the actual time maintained by the operating system.

Changing the timezone does **not** solve the problem of clock drift.

For example:

```text
Server A → 10:00
Server B → 10:03
```

Even if both servers use:

```text
Asia/Kolkata
```

they can still have different times.

This is why we need **time synchronization**.

---

# 3. `timedatectl`

Linux provides the `timedatectl` command to manage and inspect system time settings.

Check the current configuration:

```bash
timedatectl
```

Example output:

```text
Local time: Tue 2026-08-11 11:00:00 IST
Universal time: Tue 2026-08-11 05:30:00 UTC
RTC time: Tue 2026-08-11 05:30:00
Time zone: Asia/Kolkata
System clock synchronized: yes
NTP service: active
```

The important information includes:

```text
Local time
Universal time
RTC time
Time zone
System clock synchronized
NTP service
```

---

# 4. List Available Timezones

Linux provides a large number of timezone definitions.

To display them:

```bash
timedatectl list-timezones
```

You may see entries such as:

```text
Africa/Abidjan
Africa/Accra
Asia/Kolkata
Asia/Tokyo
Asia/Dubai
Europe/London
America/New_York
America/Los_Angeles
```

The system contains hundreds of timezone definitions.

To search for a particular timezone:

```bash
timedatectl list-timezones | grep Kolkata
```

Output:

```text
Asia/Kolkata
```

---

# 5. Change the System Timezone

Suppose the server currently has the wrong timezone.

We can configure it using:

```bash
timedatectl set-timezone Asia/Kolkata
```

Verify:

```bash
timedatectl
```

or:

```bash
date
```

Example:

```text
Tue Aug 11 11:05:00 IST 2026
```

### Another Example

For New York:

```bash
timedatectl set-timezone America/New_York
```

For London:

```bash
timedatectl set-timezone Europe/London
```

---

# 6. Why Timezone Configuration Alone Is Not Enough

A common misunderstanding is:

> "If I configure the correct timezone, my server time will always remain correct."

This is not true.

The timezone only tells Linux how to represent the time.

The physical/system clock can gradually drift.

For example:

```text
Day 1
Server A → 10:00:00
Server B → 10:00:00

After some time

Server A → 10:00:05
Server B → 09:59:58
```

The difference may appear small, but in large distributed environments even small differences can become important.

Manual time correction is therefore not a production-grade solution.

We need an automatic synchronization mechanism.

That is where **NTP and Chrony** come in.

---

# 7. Chrony

Modern Linux systems commonly use **Chrony** for time synchronization.

Chrony is a time synchronization implementation designed to keep the system clock synchronized with remote time sources.

Think of the architecture as three important components:

```text
                Time Server
                     |
                     |
              Network / NTP
                     |
                     v
              +-------------+
              |   chronyd   |
              |   service   |
              +-------------+
                     |
                     v
              System Clock
```

There are three things we need to understand:

```text
1. chrony package
2. chronyd service
3. /etc/chrony.conf
```

---

# 8. Chrony Package

First, the Chrony software must be installed.

On RHEL-based systems:

```bash
dnf install chrony -y
```

On systems where `yum` is used:

```bash
yum install chrony -y
```

Check whether it is installed:

```bash
rpm -q chrony
```

---

# 9. `chronyd` Service

`chronyd` is the daemon/service responsible for performing time synchronization.

Check its status:

```bash
systemctl status chronyd
```

Start it:

```bash
systemctl start chronyd
```

Enable it during boot:

```bash
systemctl enable chronyd
```

We can also use:

```bash
systemctl enable --now chronyd
```

This performs both operations:

```text
enable → start automatically after reboot
now    → start immediately
```

Verify:

```bash
systemctl status chronyd
```

---

# 10. Chrony Configuration File

The main Chrony configuration file is:

```text
/etc/chrony.conf
```

Open it using:

```bash
vim /etc/chrony.conf
```

or:

```bash
vi /etc/chrony.conf
```

This file contains configuration related to time sources and synchronization behavior.

---

# 11. Configure NTP Servers

Inside `/etc/chrony.conf`, we can define time sources.

For example:

```text
server time.example.com iburst
```

Multiple servers can be configured:

```text
server server1.example.com iburst
server server2.example.com iburst
server server3.example.com iburst
```

We can also use an NTP pool:

```text
pool pool.ntp.org iburst
```

### What Is `iburst`?

`iburst` helps Chrony synchronize quickly when communication with the time source is initially established.

Instead of slowly making initial measurements, Chrony performs a burst of measurements.

So:

```text
server time.example.com iburst
```

is commonly used when configuring a time source.

---

# 12. Restart Chrony After Configuration

After modifying:

```text
/etc/chrony.conf
```

restart the service:

```bash
systemctl restart chronyd
```

Then check:

```bash
systemctl status chronyd
```

---

# 13. Verify Time Synchronization

One of the most useful commands is:

```bash
chronyc sources -v
```

This displays the configured time sources and their synchronization status.

Example:

```text
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* time.example.com             2     6   377    20   +123us
^+ server2.example.com          2     6   377    30    -45us
^- server3.example.com          3     6   377    25   +210us
```

Important symbols include:

```text
* → currently selected source
+ → usable source
- → source not currently selected
```

The `*` is particularly important because it indicates the current synchronization source.

---

# 14. NTP Configuration Flow

The complete process can be remembered as:

```text
Install Chrony
      ↓
Enable/Start chronyd
      ↓
Edit /etc/chrony.conf
      ↓
Configure NTP server/pool
      ↓
Use iburst
      ↓
Restart chronyd
      ↓
Verify with chronyc sources -v
```

### Commands Together

```bash
dnf install chrony -y

systemctl enable --now chronyd

vim /etc/chrony.conf

systemctl restart chronyd

chronyc sources -v
```

---

# 15. Linux Performance Tuning

Now we move from **time management** to **system performance**.

Performance tuning means adjusting the system so that it behaves better for a particular workload.

Different systems have different requirements.

For example:

```text
Database Server
Web Server
Virtual Machine
File Server
High-performance application server
Laptop
```

They may not require the same performance configuration.

---

# 16. Understanding System Tuning With Real-Life Examples

Think about a radio.

A radio has a frequency dial.

If you want a particular station, you tune the frequency.

Similarly, Linux has many system parameters that can be adjusted according to the workload.

Another example is a mobile phone.

When the battery becomes very low, the phone may activate:

```text
Power Saving Mode
```

The phone changes its behavior to save battery.

Similarly, Linux can use different tuning profiles depending on the objective.

Another example is a motorcycle.

If the priority is:

```text
Maximum mileage
```

we tune the vehicle differently.

If the priority is:

```text
Maximum performance
```

we configure it differently.

Linux tuning works in a similar way.

---

# 17. What Is `tuned`?

`tuned` is a Linux service/framework used to apply performance tuning profiles.

Instead of manually changing many system parameters one by one, we can select a suitable profile.

The architecture can be thought of as:

```text
Workload
   ↓
Select Tuning Profile
   ↓
tuned
   ↓
System Parameters
   ↓
Optimized System Behavior
```

---

# 18. Install Tuned

On RHEL-based systems:

```bash
dnf install tuned -y
```

Start and enable it:

```bash
systemctl enable --now tuned
```

Check its status:

```bash
systemctl status tuned
```

---

# 19. Check the Active Tuning Profile

Use:

```bash
tuned-adm active
```

Example:

```text
Current active profile: virtual-guest
```

This tells us which profile is currently active.

---

# 20. List Available Profiles

Use:

```bash
tuned-adm list
```

This displays available tuning profiles.

Depending on the Linux version, examples may include:

```text
balanced
powersave
throughput-performance
latency-performance
virtual-guest
virtual-host
```

The exact available profiles can vary between distributions and versions.

---

# 21. Ask Tuned to Recommend a Profile

Instead of manually deciding which profile to use, we can ask `tuned`:

```bash
tuned-adm recommend
```

Example:

```text
virtual-guest
```

The recommendation is based on the detected system environment.

---

# 22. Change the Tuning Profile

The general syntax is:

```bash
tuned-adm profile <profile-name>
```

For example:

```bash
tuned-adm profile virtual-guest
```

Or:

```bash
tuned-adm profile throughput-performance
```

Verify:

```bash
tuned-adm active
```

---

# 23. Important Tuned Profiles

## `virtual-guest`

This profile is intended for a system running as a virtual machine/guest.

Example:

```text
VM running inside VMware
VM running inside KVM
VM running inside a cloud environment
```

If your Linux server is a virtual machine, `virtual-guest` can be an appropriate profile.

---

## `power-save`

This profile focuses on reducing power consumption.

The priority becomes:

```text
Power saving
      >
Maximum performance
```

This can be useful where power consumption is more important than maximum performance.

---

## `throughput-performance`

This profile focuses on achieving high throughput.

The priority becomes:

```text
High throughput
      >
Power saving
```

It can be useful for workloads where processing a large amount of data efficiently is important.

---

# 24. Static vs Dynamic Tuning

### Static Tuning

Static tuning means the system configuration is adjusted according to a predefined profile.

For example:

```text
Select profile
      ↓
Apply configuration
      ↓
System operates with that configuration
```

The configuration does not continuously change based on every workload variation.

### Dynamic Tuning

Dynamic tuning means the system can adjust behavior based on changing workload conditions.

For example:

```text
Low workload
    ↓
Configuration A

High workload
    ↓
Configuration B
```

This is similar to a mobile phone automatically changing behavior when the battery becomes low.

The important idea is:

> **Static tuning applies a defined configuration, while dynamic tuning adapts according to changing conditions.**

---

# 25. TAR — Archive Management

Now we move to another important Linux administration topic:

```text
tar
```

`tar` stands for:

> **Tape Archive**

Despite its historical name, `tar` is widely used today to create archives of files and directories.

---

# 26. Why Do We Need TAR?

Suppose we have:

```text
application/
├── file1
├── file2
├── file3
├── logs/
├── config/
└── scripts/
```

Sending hundreds or thousands of individual files over the network is inconvenient.

Instead, we can combine them:

```text
application/
      ↓
application.tar
```

Now we have one file containing the complete directory structure.

This is called an **archive**.

---

# 27. Archive vs Compression

These two concepts are different.

### Archiving

Combines multiple files/directories into one file.

```text
10 files
   ↓
archive.tar
```

### Compression

Reduces the size of data.

```text
28 MB
 ↓
6 MB
```

`tar` itself primarily performs **archiving**.

Compression algorithms such as:

```text
gzip
bzip2
xz
```

can be combined with TAR.

Therefore:

```text
tar + gzip
tar + bzip2
tar + xz
```

are commonly used.

---

# 28. Why Create Tarballs?

A TAR archive is useful for:

### Storage

Multiple files can be packaged into a single archive.

### Network Transfer

Instead of transferring many files:

```text
file1
file2
file3
...
file1000
```

we can transfer:

```text
backup.tar
```

### Backup

A directory structure can be preserved inside an archive.

### Distribution

Software/source code can be distributed as a single package.

### Protection Against Direct Modification

Instead of exposing a collection of files directly, we can package them into an archive for controlled distribution or backup.

Compression can additionally reduce storage and transfer requirements.

---

# 29. Check Directory Size Before Compression

Before creating an archive, it is useful to know the original size.

Use:

```bash
du -sh /etc
```

Example:

```text
28M    /etc
```

Here:

```text
du → disk usage
-s → summary
-h → human-readable
```

So:

```bash
du -sh
```

means:

> Show the total disk usage in a human-readable format.

---

# 30. `du -sch`

Another useful command is:

```bash
du -sch /etc
```

The options mean:

```text
-s → summary
-c → produce a grand total
-h → human-readable
```

This is useful when checking multiple files/directories and their total size.

---

# 31. Basic TAR Syntax

The basic syntax is:

```bash
tar [options] archive-name files/directories
```

For example:

```bash
tar -cvf backup.tar /etc
```

Let's understand each option.

```text
-c → create
-v → verbose
-f → archive file
```

Therefore:

```bash
tar -cvf backup.tar /etc
```

means:

> Create a TAR archive named `backup.tar` containing `/etc`.

---

# 32. Important TAR Options

| Option | Meaning                                               |
| ------ | ----------------------------------------------------- |
| `-c`   | Create archive                                        |
| `-x`   | Extract archive                                       |
| `-v`   | Verbose output                                        |
| `-f`   | Specify archive filename                              |
| `-t`   | List/test archive contents                            |
| `-C`   | Change to a directory before performing the operation |
| `-z`   | Use gzip compression                                  |
| `-j`   | Use bzip2 compression                                 |
| `-J`   | Use xz compression                                    |

Remember:

```text
c → create
x → extract
t → test/list
v → verbose
f → file
```

---

# 33. Create a TAR Archive

Command:

```bash
tar -cvf backup.tar /etc
```

This creates:

```text
backup.tar
```

containing the `/etc` directory.

---

# 34. List TAR Archive Contents

Before extracting an archive, we can inspect its contents.

Use:

```bash
tar -tvf backup.tar
```

Here:

```text
-t → list contents
-v → verbose
-f → archive file
```

This allows us to see what is stored inside the archive.

---

# 35. Extract a TAR Archive

Use:

```bash
tar -xvf backup.tar
```

Here:

```text
-x → extract
-v → verbose
-f → archive file
```

The archive contents will be extracted into the current directory.

---

# 36. Extract TAR to a Specific Location

Sometimes we don't want to extract into the current directory.

We can use capital:

```text
-C
```

Example:

```bash
tar -xvf backup.tar -C /tmp/
```

This extracts the archive into:

```text
/tmp/
```

### Important

Do not confuse:

```text
-C
```

with:

```text
-c
```

They have completely different meanings.

```text
-c → create
-C → change directory
```

Linux options are case-sensitive.

---

# 37. TAR + Gzip

A normal TAR archive may still be large.

We can combine TAR with gzip.

Use:

```bash
tar -czvf backup.tar.gz /etc
```

Here:

```text
-c → create
-z → gzip
-v → verbose
-f → filename
```

The result is:

```text
backup.tar.gz
```

---

# 38. Extract Gzip TAR

Use:

```bash
tar -xzvf backup.tar.gz
```

Here:

```text
-x → extract
-z → gzip
-v → verbose
-f → file
```

To extract into another location:

```bash
tar -xzvf backup.tar.gz -C /tmp/
```

---

# 39. TAR + Bzip2

Bzip2 compression is enabled with:

```text
-j
```

Create:

```bash
tar -cjvf backup.tar.bz2 /etc
```

Extract:

```bash
tar -xjvf backup.tar.bz2
```

The important point is:

```text
-j → bzip2
```

---

# 40. TAR + Xz

Xz compression is enabled using:

```text
-J
```

Notice that it is **capital J**.

Create:

```bash
tar -cJvf backup.tar.xz /etc
```

Extract:

```bash
tar -xJvf backup.tar.xz
```

The important point is:

```text
-J → xz
```

---

# 41. Compression Comparison

In the practical demonstration, the original `/etc` directory was approximately:

```text
28 MB
```

The approximate results were:

| Method          | TAR Option | Approx. Size |
| --------------- | ---------- | -----------: |
| Original `/etc` | —          |       ~28 MB |
| Gzip            | `-z`       |      ~6.2 MB |
| Bzip2           | `-j`       |      ~5.4 MB |
| Xz              | `-J`       |      ~4.4 MB |

The important observation is:

```text
Original
   ↓
28 MB

gzip
   ↓
~6.2 MB

bzip2
   ↓
~5.4 MB

xz
   ↓
~4.4 MB
```

So, in this particular demonstration, **XZ produced the smallest archive**.

However, compression ratio is not the only consideration in real production environments. Compression time, CPU usage, decompression speed, and workload requirements also matter.

---

# 42. Complete TAR Command Revision

### Create normal TAR

```bash
tar -cvf backup.tar /etc
```

### List contents

```bash
tar -tvf backup.tar
```

### Extract

```bash
tar -xvf backup.tar
```

### Extract to specific location

```bash
tar -xvf backup.tar -C /tmp/
```

### Create Gzip archive

```bash
tar -czvf backup.tar.gz /etc
```

### Extract Gzip

```bash
tar -xzvf backup.tar.gz
```

### Create Bzip2 archive

```bash
tar -cjvf backup.tar.bz2 /etc
```

### Extract Bzip2

```bash
tar -xjvf backup.tar.bz2
```

### Create Xz archive

```bash
tar -cJvf backup.tar.xz /etc
```

### Extract Xz

```bash
tar -xJvf backup.tar.xz
```

---

# 43. Day 31 Quick Revision

The complete session can be remembered in three major sections:

```text
DAY 31
│
├── NTP / Time Management
│   ├── Time synchronization
│   ├── Timezone
│   ├── timedatectl
│   ├── timedatectl list-timezones
│   ├── set-timezone
│   ├── chrony
│   ├── chronyd
│   ├── /etc/chrony.conf
│   ├── server/pool
│   ├── iburst
│   └── chronyc sources -v
│
├── Performance Tuning
│   ├── System tuning
│   ├── Static tuning
│   ├── Dynamic tuning
│   ├── tuned
│   ├── tuned-adm active
│   ├── tuned-adm list
│   ├── tuned-adm recommend
│   ├── tuned-adm profile
│   ├── virtual-guest
│   ├── power-save
│   └── throughput-performance
│
└── TAR
    ├── Archive
    ├── Compression
    ├── du -sh
    ├── du -sch
    ├── -c
    ├── -x
    ├── -v
    ├── -f
    ├── -t
    ├── -C
    ├── gzip → -z
    ├── bzip2 → -j
    └── xz → -J
```

## Important Commands to Remember

```bash
# Time
timedatectl
timedatectl list-timezones
timedatectl set-timezone Asia/Kolkata

# Chrony
dnf install chrony -y
systemctl enable --now chronyd
systemctl restart chronyd
systemctl status chronyd
chronyc sources -v

# Tuned
dnf install tuned -y
systemctl enable --now tuned
tuned-adm active
tuned-adm list
tuned-adm recommend
tuned-adm profile virtual-guest

# Disk usage
du -sh /etc
du -sch /etc

# TAR
tar -cvf backup.tar /etc
tar -tvf backup.tar
tar -xvf backup.tar
tar -xvf backup.tar -C /tmp/

# Gzip
tar -czvf backup.tar.gz /etc
tar -xzvf backup.tar.gz

# Bzip2
tar -cjvf backup.tar.bz2 /etc
tar -xjvf backup.tar.bz2

# Xz
tar -cJvf backup.tar.xz /etc
tar -xJvf backup.tar.xz
```

### Final Concept

The three topics of Day 31 solve three different administration problems:

```text
NTP / Chrony
      ↓
Keep server time synchronized

Tuned
      ↓
Optimize system behavior for a workload

TAR
      ↓
Archive and compress files/directories
```

So the practical administrator mindset is:

> **Keep the system synchronized → tune it for the workload → efficiently archive and transfer its data.**
