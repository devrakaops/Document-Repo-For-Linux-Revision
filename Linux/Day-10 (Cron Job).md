# Day-10 Cron Job in Linux

## What is a Cron Job?

In Linux administration, many tasks need to be performed again and again.

For example:

* Taking database backups
* Taking website backups
* Cleaning old log files
* Running a script
* Generating reports
* Removing temporary files
* Performing regular maintenance
* Checking server status
* Running security or maintenance commands

If we perform these tasks manually every time, it takes time and there is also a chance that we may forget to perform the task.

Linux provides a way to automatically run these tasks at a specific time.

This mechanism is called a **Cron Job**.

> **A Cron Job is a scheduled task that automatically runs a command or script at a specific time or at a specific interval.**

Once we configure a cron job, Linux automatically runs the command or script according to the schedule.

---

# Why Do We Need Cron Jobs?

Let's understand this with a real-world example.

Imagine that you work for a **Web Hosting Company**.

Your company hosts websites for many clients.

As a Linux administrator, you are responsible for:

* Client websites
* Client databases
* Server maintenance
* Website backups
* Database backups
* Log management
* Other regular maintenance tasks

Suppose your company has a backup contract with different clients.

For example:

```text
Client A → Daily Backup
Client B → Weekly Backup
Client C → Monthly Backup
```

You have created a backup script:

```bash
/root/scripts/backup.sh
```

Suppose this script contains some Linux commands that are used to take a backup of the server's **website and database**.

# What is a Script?

For someone who does not know what a script means:

> A **script is a simple text file that contains executable Linux commands.**

For example:

```bash
linux-command-1
linux-command-2
linux-command-3
.
.
linux-command-N
```

When we execute the script, Linux runs these commands one by one using the **Bash shell**.

So, our `backup.sh` script may contain commands that perform:

```text
Website Backup
      +
Database Backup
      ↓
Backup Files
```

Now suppose the company policy says:

> Take the backup every day at 2:00 AM.

Without a cron job, you would have to come to the office every night at 2:00 AM and manually run:

```bash
/root/scripts/backup.sh
```

This is obviously not a good way to manage servers.

Imagine having 50, 100, or 500 clients.

You cannot sit in front of the server every night and run backup commands manually.

This is where **Cron Job** becomes useful.

We can tell Linux:

> "Run this backup script every day at 2:00 AM."

For example:

```text
Every Day
    ↓
2:00 AM
    ↓
Run backup script
```

Linux will automatically run the script.

We do not need to come to the office at 2:00 AM.

This is the main reason we use cron jobs.

---

# Real-World Example

Suppose our backup script is:

```bash
/root/scripts/backup.sh
```

We want this script to run every day at 2:00 AM.

We can create the following cron job:

```text
0 2 * * * /root/scripts/backup.sh
```

The meaning is:

```text
Every Day
    ↓
At 2:00 AM
    ↓
Run /root/scripts/backup.sh
```

So the administrator does not need to manually run the backup.

Linux will automatically execute it.

---

# Cron vs Crontab

These two terms are commonly used together, but they are not exactly the same.

## What is Cron?

**Cron** is a time-based job scheduling mechanism in Linux.

Its job is to automatically execute commands and scripts according to a schedule.

In simple words:

> **Cron is responsible for running scheduled tasks automatically.**

---

## What is Crontab?

`crontab` stands for **Cron Table**.

It is used to define and manage cron jobs.

For example:

```text
0 2 * * * /root/scripts/backup.sh
```

This is a **crontab entry**.

We normally use:

```bash
crontab -e
```

to create or edit cron jobs.

A simple way to understand it:

```text
Crontab
   ↓
Defines the schedule
   ↓
Cron / crond
   ↓
Runs the scheduled command
```

---

# Cron Package

On RHEL-based Linux systems such as:

* RHEL
* CentOS
* Rocky Linux
* AlmaLinux

cron functionality is normally provided by the **cronie** package.

We can check whether the package is installed:

```bash
rpm -q cronie
```

If it is not installed, we can install it using:

```bash
dnf install cronie
```

---

# Cron Service — crond

The cron scheduler runs as a background service.

On RHEL-based systems, the service is called:

```text
crond
```

The `crond` service continuously runs in the background and checks the configured cron jobs.

When the scheduled time arrives, `crond` runs the required command or script.

We can think of it like this:

```text
crontab
   ↓
Schedule
   ↓
crond
   ↓
Checks the time
   ↓
Runs the command
```

---

# Check Cron Service

To check whether the cron service is running:

```bash
systemctl status crond
```

If the service is running, we should see:

```text
Active: active (running)
```

---

# Start Cron Service

If the `crond` service is stopped, we can start it:

```bash
systemctl start crond
```

---

# Enable Cron Service

We normally want cron to start automatically after a server reboot.

For this:

```bash
systemctl enable crond
```

We can also enable and start it at the same time:

```bash
systemctl enable --now crond
```

---

# Who Can Create Cron Jobs?

Cron jobs are **user-specific**.

Different Linux users can have their own cron jobs.

For example:

```text
root
student
developer
backup
oracle
```

Each user can have their own scheduled tasks.

So cron jobs are not only for the root user.

---

# Creating a Cron Job for the Current User

To create or edit the current user's cron jobs:

```bash
crontab -e
```

The `-e` option means:

> Edit the crontab.

For example, if you are logged in as:

```text
student
```

and run:

```bash
crontab -e
```

you are editing the cron jobs of the `student` user.

If you are logged in as root:

```bash
crontab -e
```

you are editing the root user's cron jobs.

---

# Root User Managing Another User's Cron Job

The root user can also manage another user's crontab.

The syntax is:

```bash
crontab -u username -e
```

For example:

```bash
crontab -u student -e
```

This means:

> The root user is editing the `student` user's crontab.

Another example:

```bash
crontab -u backup -e
```

This edits the cron jobs of the `backup` user.

To view another user's cron jobs:

```bash
crontab -u student -l
```

---

# List Cron Jobs

To see the cron jobs of the current user:

```bash
crontab -l
```

The `-l` option means:

> List the cron jobs.

Example output:

```text
0 2 * * * /root/scripts/backup.sh
```

---

# Remove Cron Jobs

To remove the current user's complete crontab:

```bash
crontab -r
```

**Be careful with this command.**

`crontab -r` can remove the complete crontab of the current user.

So do not use it without understanding what you are doing.

If you only want to remove one job, use:

```bash
crontab -e
```

and remove that particular line.

---

# Location of User Cron Files

User-specific cron files are normally stored under:

```text
/var/spool/cron/
```

We can check this directory:

```bash
ls -l /var/spool/cron/
```

You may see files based on usernames.

For example:

```text
/var/spool/cron/root
/var/spool/cron/student
```

These files contain the cron jobs for the respective users.

However, we should normally **not manually edit these files**.

The recommended way is:

```bash
crontab -e
```

---

# Important: `/var/spool/cron` vs `/var/spool/mail`

Do not confuse these two locations.

Cron user files are normally stored under:

```text
/var/spool/cron/
```

For example:

```text
/var/spool/cron/root
```

`/var/spool/mail/` is related to **user mail**, not the normal location of user crontab files.

So remember:

```text
Cron files
    ↓
/var/spool/cron/
```

---

# System-Wide Cron Configuration

Linux also has a system-wide cron configuration file:

```text
/etc/crontab
```

This file can contain system-level cron jobs.

There is an important difference between a normal user's crontab and `/etc/crontab`.

A normal user's crontab looks like:

```text
* * * * * command
```

But `/etc/crontab` also contains the **username**.

Example:

```text
0 2 * * * root /root/scripts/backup.sh
```

Here:

```text
0                    → Minute
2                    → Hour
*                    → Day of Month
*                    → Month
*                    → Day of Week
root                 → User
/root/scripts/...    → Command
```

The `root` field tells the system which user should execute the command.

---

# `/etc/cron.d/`

Another important directory is:

```text
/etc/cron.d/
```

Applications or administrators can place system-level cron configuration files here.

For example:

```bash
ls -l /etc/cron.d/
```

You may find configuration files related to different applications or system tasks.

---

# Periodic Cron Directories

Linux can also have directories for commonly used time intervals:

```text
/etc/cron.hourly/
/etc/cron.daily/
/etc/cron.weekly/
/etc/cron.monthly/
```

Their purpose is easy to understand from their names.

```text
cron.hourly
    ↓
Hourly tasks

cron.daily
    ↓
Daily tasks

cron.weekly
    ↓
Weekly tasks

cron.monthly
    ↓
Monthly tasks
```

---

# Cron Job Format

The most important thing to understand in cron is its format.

A normal user crontab entry looks like:

```text
* * * * * command
```

There are **five time fields** before the command.

```text
┌──────── Minute
│ ┌────── Hour
│ │ ┌──── Day of Month
│ │ │ ┌── Month
│ │ │ │ ┌ Day of Week
│ │ │ │ │
* * * * * command
```

After these five fields, we write the command or script that we want to execute.

---

# Five Fields of Cron

## 1. Minute

Range:

```text
0-59
```

It tells cron which minute of the hour the command should run.

Example:

```text
30
```

means:

> Run at the 30th minute.

---

## 2. Hour

Range:

```text
0-23
```

Linux uses the 24-hour format.

Example:

```text
2
```

means:

> 2:00 AM

Example:

```text
14
```

means:

> 2:00 PM

---

## 3. Day of Month

Range:

```text
1-31
```

This represents the day of the month.

Example:

```text
15
```

means:

> The 15th day of the month.

---

## 4. Month

Range:

```text
1-12
```

For example:

```text
1  → January
2  → February
3  → March
...
12 → December
```

---

## 5. Day of Week

Common numeric values are:

```text
0 → Sunday
1 → Monday
2 → Tuesday
3 → Wednesday
4 → Thursday
5 → Friday
6 → Saturday
```

Some cron implementations also allow:

```text
7 → Sunday
```

---

# Asterisk `*`

The `*` symbol is very important in cron.

It means:

> **Every possible value.**

For example:

```text
*
```

in the minute field means:

> Every minute.

So:

```text
* * * * * command
```

means:

> Run the command every minute.

---

# Common Cron Examples

## Run a Command Every Minute

```text
* * * * * /path/to/script.sh
```

Meaning:

> Run the script every minute.

---

## Run a Command Every Hour

```text
0 * * * * /path/to/script.sh
```

Meaning:

> Run the script at the 0th minute of every hour.

For example:

```text
01:00
02:00
03:00
04:00
05:00
```

---

# Run a Command Every Day at 2:00 AM

```text
0 2 * * * /root/scripts/backup.sh
```

Breakdown:

```text
0 → Minute
2 → Hour
* → Every day of month
* → Every month
* → Every day of week
```

Meaning:

> **Run `/root/scripts/backup.sh` every day at 2:00 AM.**

---

# Run a Command Every Day at 11:30 PM

```text
30 23 * * * /root/scripts/backup.sh
```

Meaning:

> Run the backup script every day at 11:30 PM.

---

# Weekly Cron Job

Suppose we want to run the backup every Sunday at 2:00 AM.

```text
0 2 * * 0 /root/scripts/backup.sh
```

Breakdown:

```text
0 → Minute
2 → Hour
* → Every day of month
* → Every month
0 → Sunday
```

Meaning:

> **Run the backup every Sunday at 2:00 AM.**

---

# Monthly Cron Job

Suppose we want to take a backup on the **1st day of every month at 2:00 AM**.

```text
0 2 1 * * /root/scripts/backup.sh
```

Meaning:

```text
Every month
    ↓
1st day
    ↓
2:00 AM
    ↓
Run backup
```

---

# Multiple Values

We can provide multiple values in a cron field.

For example:

```text
0 2 * * 1,3,5 /root/scripts/backup.sh
```

Here:

```text
1 → Monday
3 → Wednesday
5 → Friday
```

So the command runs:

> Every Monday, Wednesday, and Friday at 2:00 AM.

---

# Range

We can use `-` to specify a range.

Example:

```text
0 2 * * 1-5 /root/scripts/backup.sh
```

Here:

```text
1 → Monday
5 → Friday
```

So the command runs:

> Monday to Friday at 2:00 AM.

---

# Step Value

We can use `/` to define an interval.

Example:

```text
*/10 * * * * /root/scripts/check.sh
```

Meaning:

> Run the script every 10 minutes.

The command will run around:

```text
00
10
20
30
40
50
```

minutes of every hour.

---

# Why Should We Use the Full Path of a Command?

In cron jobs, it is a good practice to use the **absolute path** of commands and scripts.

Suppose we want to use the `tar` command.

First, find its location:

```bash
which tar
```

Example output:

```text
/usr/bin/tar
```

Now we can use:

```text
/usr/bin/tar
```

in our cron job.

Similarly, we can check:

```bash
which mysqldump
```

or:

```bash
which rsync
```

This gives us the location of the command.

---

# Why is the Full Path Important?

When we run a command manually:

```bash
tar
```

our shell uses environment variables such as `PATH` to find the command.

But cron does not run in exactly the same environment as our normal interactive terminal.

Because of this, a command that works manually may sometimes fail when used in a cron job if its path is not available.

Therefore, using an absolute path is a safer and more reliable practice.

Instead of:

```text
tar -czf /backup/site.tar.gz /var/www/html
```

we can use:

```text
/usr/bin/tar -czf /backup/site.tar.gz /var/www/html
```

if `/usr/bin/tar` is the actual path.

---

# Running a Script Through Cron

Suppose we have created a backup script:

```text
/root/scripts/backup.sh
```

First, make sure the script has execute permission:

```bash
chmod +x /root/scripts/backup.sh
```

Now test the script manually:

```bash
/root/scripts/backup.sh
```

This step is important.

If the script itself does not work manually, it will not magically work through cron.

Once the script is working correctly, edit the crontab:

```bash
crontab -e
```

Add:

```text
0 2 * * * /root/scripts/backup.sh
```

Now the script will run automatically every day at 2:00 AM.

---

# Redirecting Cron Output to a Log File

Sometimes a cron job runs, but we do not know whether the script was successful or failed.

For this reason, it is useful to save the output and errors into a log file.

Example:

```text
0 2 * * * /root/scripts/backup.sh >> /var/log/backup.log 2>&1
```

Let's understand this part:

```text
>> /var/log/backup.log
```

This sends the normal output to:

```text
/var/log/backup.log
```

And:

```text
2>&1
```

sends the error output to the same log file.

So both normal output and errors are stored in:

```text
/var/log/backup.log
```

We can check the log using:

```bash
cat /var/log/backup.log
```

Or:

```bash
tail -f /var/log/backup.log
```

---

# Complete Real-World Backup Example

Let's take the hosting company example again.

Suppose you manage many client websites.

You create a script:

```text
/root/scripts/client-backup.sh
```

The script performs:

```text
Website Backup
       +
Database Backup
       ↓
Backup Files
```

Your company policy says:

> Take the backup every day at 2:00 AM.

First, test the script manually:

```bash
/root/scripts/client-backup.sh
```

If everything works correctly, edit the cron table:

```bash
crontab -e
```

Add:

```text
0 2 * * * /root/scripts/client-backup.sh >> /var/log/client-backup.log 2>&1
```

Now the complete process is:

```text
             2:00 AM
                ↓
              crond
                ↓
       client-backup.sh
                ↓
        ┌───────┴───────┐
        ↓               ↓
 Website Backup   Database Backup
        │               │
        └───────┬───────┘
                ↓
          Backup Files
                ↓
        Backup Log File
```

The administrator does not need to manually come to the office at 2:00 AM.

Linux automatically performs the task.

This is the real benefit of cron jobs.

---

# How to Verify a Cron Job

After creating a cron job, we should verify that it is configured correctly.

## Step 1 — Check the Cron Job

Run:

```bash
crontab -l
```

Example:

```text
0 2 * * * /root/scripts/client-backup.sh >> /var/log/client-backup.log 2>&1
```

If the entry is present, the cron job has been added to the user's crontab.

---

## Step 2 — Check the Cron Service

Run:

```bash
systemctl status crond
```

Make sure the service is:

```text
active (running)
```

---

## Step 3 — Test the Script Manually

Run:

```bash
/root/scripts/client-backup.sh
```

If it fails manually, fix the script first.

---

## Step 4 — Check the Script Permission

Run:

```bash
ls -l /root/scripts/client-backup.sh
```

If required:

```bash
chmod +x /root/scripts/client-backup.sh
```

---

## Step 5 — Check Command Paths

For commands used inside the script:

```bash
which tar
which mysqldump
which rsync
```

Make sure the paths used in the script are correct.

---

## Step 6 — Check the Log File

If the cron job is writing logs:

```bash
cat /var/log/client-backup.log
```

or:

```bash
tail -f /var/log/client-backup.log
```

---

## Step 7 — Check Cron Service Logs

We can check the `crond` service logs:

```bash
journalctl -u crond
```

For today's logs:

```bash
journalctl -u crond --since today
```

These logs can help us understand whether cron is running the scheduled task.

---

# Important Points While Creating Cron Jobs

When creating a cron job, remember the following points.

### Always Test the Command First

Before adding a command to cron, run it manually.

Example:

```bash
/root/scripts/backup.sh
```

If the command does not work manually, fix it first.

---

### Prefer Absolute Paths

Use:

```text
/usr/bin/tar
/usr/bin/rsync
/usr/bin/mysqldump
/root/scripts/backup.sh
```

instead of depending on the normal shell `PATH`.

---

### Check Permissions

The user running the cron job must have the required permissions.

For example, if the script needs to read:

```text
/var/www/html
```

the cron user must have permission to access it.

If the script needs to write to:

```text
/backup
```

the cron user must have permission to write there.

---

### Keep Logs

For important jobs such as backups, logging is very useful.

Example:

```text
0 2 * * * /root/scripts/backup.sh >> /var/log/backup.log 2>&1
```

If something goes wrong, the log can help us find the problem.

---

# Common Cron Commands

| Purpose                                | Command                        |
| -------------------------------------- | ------------------------------ |
| Edit current user's crontab            | `crontab -e`                   |
| List current user's cron jobs          | `crontab -l`                   |
| Remove current user's complete crontab | `crontab -r`                   |
| Edit another user's crontab            | `crontab -u username -e`       |
| List another user's crontab            | `crontab -u username -l`       |
| Check cron service                     | `systemctl status crond`       |
| Start cron service                     | `systemctl start crond`        |
| Enable cron service                    | `systemctl enable crond`       |
| Enable and start cron                  | `systemctl enable --now crond` |
| Check cron package                     | `rpm -q cronie`                |
| Check cron logs                        | `journalctl -u crond`          |

---

# Important Cron Files and Directories

| Location             | Purpose                               |
| -------------------- | ------------------------------------- |
| `/var/spool/cron/`   | User-specific cron files              |
| `/etc/crontab`       | System-wide crontab                   |
| `/etc/cron.d/`       | System-level cron configuration files |
| `/etc/cron.hourly/`  | Hourly tasks                          |
| `/etc/cron.daily/`   | Daily tasks                           |
| `/etc/cron.weekly/`  | Weekly tasks                          |
| `/etc/cron.monthly/` | Monthly tasks                         |

---

# Cron Job Troubleshooting Flow

If a cron job is not working, follow this order.

```text
Cron Job Not Working
        ↓
Check crontab
        ↓
Check crond service
        ↓
Run script manually
        ↓
Check permissions
        ↓
Check command paths
        ↓
Check log file
        ↓
Check crond logs
```

Commands:

```bash
crontab -l
```

```bash
systemctl status crond
```

```bash
/root/scripts/backup.sh
```

```bash
ls -l /root/scripts/backup.sh
```

```bash
which tar
which mysqldump
```

```bash
cat /var/log/backup.log
```

```bash
journalctl -u crond
```

This step-by-step method makes cron troubleshooting much easier.

---

# Easy Way to Remember Cron

You can think of **Cron as an automatic employee**.

You give it an instruction:

> "Run this backup every day at 2:00 AM."

You put the instruction in the crontab:

```text
0 2 * * * /root/scripts/backup.sh
```

Then `crond` takes care of the execution.

The complete flow is:

```text
User
  ↓
crontab -e
  ↓
Define Schedule
  ↓
crond Service
  ↓
Wait for Scheduled Time
  ↓
Execute Command / Script
  ↓
Task Completed
```

The administrator does not need to be present at the scheduled time.

---

# Final Summary

**Cron Job** is one of the most useful features for Linux system administration.

It helps us automate repetitive tasks.

For example:

```text
Database Backup
Website Backup
Log Cleanup
Report Generation
File Cleanup
Monitoring Scripts
Maintenance Scripts
```

Instead of manually running these tasks, we can schedule them.

The basic cron format is:

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of Week
│ │ │ └──── Month
│ │ └────── Day of Month
│ └──────── Hour
└────────── Minute
```

The most important example is:

```text
0 2 * * * /root/scripts/backup.sh
```

Meaning:

> **Run `/root/scripts/backup.sh` every day at 2:00 AM.**

The basic working can be remembered as:

```text
crontab
   ↓
Schedule
   ↓
crond
   ↓
Specified Time
   ↓
Command / Script
   ↓
Automatic Execution
```

So, the main purpose of Cron Job is:

> **To automate repetitive Linux tasks and run them automatically at the required time, without requiring manual intervention.**
