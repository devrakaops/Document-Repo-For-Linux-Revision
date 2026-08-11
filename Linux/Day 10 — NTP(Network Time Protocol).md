# NTP — Network Time Protocol

## Introduction

Time is very important in a Linux environment.

In a small environment, a few seconds of time difference may not look like a big problem. But in a production environment, many servers communicate with each other.

For example:

```text
Web Server
     ↓
Application Server
     ↓
Database Server
     ↓
Monitoring Server
```

If the system clocks of these servers are not synchronized, it can create problems while checking logs, troubleshooting issues, tracking transactions, and investigating security events.

This is why we use **NTP — Network Time Protocol**.

> **NTP is used to keep the system time of a Linux machine accurate and synchronized with a reliable time source.**

---

# Why Do We Need NTP?

Suppose we have three servers:

```text
Server 1
Server 2
Server 3
```

Now suppose their clocks are different:

```text
Server 1 → 10:00:00
Server 2 → 10:03:00
Server 3 → 09:58:00
```

Now a user performs a transaction.

Different servers may record the same transaction at different times:

```text
Server 1 → Request received at 10:01
Server 2 → Request processed at 09:59
Server 3 → Database updated at 10:00
```

Now the administrator may have difficulty understanding:

* Which event happened first?
* Which server processed the request first?
* When did the transaction actually start?
* Which log entry is the correct sequence?

This is where time synchronization becomes important.

With NTP, the servers can synchronize their system clocks with a reliable time source.

```text
Reliable Time Source
        ↓
      NTP
        ↓
Linux Server
        ↓
System Clock
```

---

# Where Is NTP Used?

NTP is useful in many production environments.

Common examples include:

* Web servers
* Application servers
* Database servers
* Monitoring servers
* Load balancers
* Firewalls
* Distributed applications
* Database replication
* Kubernetes environments
* Security systems
* Log analysis
* Troubleshooting

For example, during troubleshooting, an administrator may need to compare logs from:

```text
Web Server
Application Server
Database Server
Firewall
Load Balancer
```

If the system clocks are synchronized, it becomes much easier to understand the sequence of events.

---

# How Does NTP Work?

The basic idea is simple.

A Linux server communicates with an NTP time source over the network.

The NTP source provides a reliable time reference.

The Linux system then uses that information to keep its system clock synchronized.

The basic flow is:

```text
              NTP Time Source
                    |
                    |
                 Network
                    |
                    ↓
              Linux Server
                    |
                    ↓
              System Clock
```

In a larger environment:

```text
                 NTP Server
                     |
          +----------+----------+
          |          |          |
          ↓          ↓          ↓
       Server 1   Server 2   Server 3
          |          |          |
          ↓          ↓          ↓
      System     System     System
       Clock      Clock      Clock
```

The main purpose is to maintain **accurate and synchronized system time**.

---

# NTP Time Sources

A Linux system can use different types of NTP sources.

Common options are:

```text
Single NTP Server
        OR
Multiple NTP Servers
        OR
NTP Pool
```

The choice depends on the environment.

---

## Single NTP Server

We can configure one NTP server.

For example:

```text
server ntp1.example.com iburst
```

The architecture is:

```text
NTP Server
     ↓
Linux Server
     ↓
System Clock
```

This setup is simple.

However, there is one limitation.

If the NTP server becomes unavailable:

```text
Linux Server
     ↓
NTP Server
     X
   DOWN
```

the Linux server cannot get new time updates from that source.

For a simple environment, one source may be sufficient.

For important production environments, multiple reliable sources are generally preferred.

---

# Multiple NTP Servers

We can configure multiple NTP servers:

```text
server ntp1.example.com iburst
server ntp2.example.com iburst
server ntp3.example.com iburst
```

Now the Linux server has multiple time sources.

The architecture can be understood like this:

```text
       NTP Server 1
             \
       NTP Server 2 ----→ Linux Server
             /
       NTP Server 3
```

If one source becomes unavailable, other configured sources can still be available.

Chrony can evaluate the available sources and select a suitable source for synchronization.

This provides better reliability than depending on only one NTP server.

---

# NTP Pool

Another option is an **NTP Pool**.

Example:

```text
pool pool.ntp.org iburst
```

An NTP pool represents a group of available NTP servers.

Instead of manually configuring every individual server, we can configure the pool.

Conceptually:

```text
                 NTP Pool
              /     |      \
             ↓      ↓       ↓
          Server  Server  Server
              \     |     /
               \    |    /
                   ↓
             Linux Server
                   ↓
             System Clock
```

So remember:

```text
Single Server
    ↓
One time source

Multiple Servers
    ↓
Several configured time sources

NTP Pool
    ↓
Group of available time sources
```

---

# Chrony — NTP Synchronization in Linux

Modern RHEL-based Linux systems commonly use **Chrony** for time synchronization.

Chrony provides the software required to synchronize the Linux system clock with NTP time sources.

There are four important things to remember:

```text
chrony
   ↓
Package

chronyd
   ↓
Service / Daemon

/etc/chrony.conf
   ↓
Configuration File

chronyc
   ↓
Command-line Tool
```

The complete flow is:

```text
NTP Server / NTP Pool
          ↓
       Network
          ↓
       chronyd
          ↓
    System Clock
```

---

# Installing Chrony

First, install the Chrony package.

On RHEL-based systems using `dnf`:

```bash
dnf install chrony -y
```

On systems where `yum` is used:

```bash
yum install chrony -y
```

To check whether Chrony is already installed:

```bash
rpm -q chrony
```

If Chrony is installed, the command displays the installed package information.

---

# Managing the `chronyd` Service

`chronyd` is the service/daemon responsible for performing time synchronization.

## Check the Service

```bash
systemctl status chronyd
```

This tells us whether the Chrony service is running.

---

## Start the Service

If the service is not running:

```bash
systemctl start chronyd
```

---

## Enable Chrony at Boot

To make sure Chrony starts automatically after a reboot:

```bash
systemctl enable chronyd
```

---

## Start Now and Enable at Boot

We can perform both operations together:

```bash
systemctl enable --now chronyd
```

Here:

```text
enable
    ↓
Start automatically after reboot

--now
    ↓
Start immediately
```

Therefore:

```bash
systemctl enable --now chronyd
```

means:

> Start Chrony now and also make sure it starts automatically after every reboot.

---

# Chrony Configuration File

The main Chrony configuration file is:

```text
/etc/chrony.conf
```

Open the file using:

```bash
vim /etc/chrony.conf
```

or:

```bash
vi /etc/chrony.conf
```

This file is used to configure the NTP servers or NTP pool that the Linux server should use.

---

# Configure a Single NTP Server

Suppose we want to use one NTP server.

Open:

```text
/etc/chrony.conf
```

Add:

```text
server ntp1.example.com iburst
```

Here:

```text
server
    ↓
Tells Chrony to use an NTP server

ntp1.example.com
    ↓
Hostname of the NTP server

iburst
    ↓
Helps with faster initial synchronization
```

---

# Configure Multiple NTP Servers

We can also configure multiple servers:

```text
server ntp1.example.com iburst
server ntp2.example.com iburst
server ntp3.example.com iburst
```

Now Chrony has multiple time sources available.

This is generally better for reliability because the Linux server does not depend on only one time source.

---

# Configure an NTP Pool

Instead of configuring individual servers, we can configure an NTP pool:

```text
pool pool.ntp.org iburst
```

This allows Chrony to use available servers from the configured pool.

---

# Understanding `iburst`

You will commonly see `iburst` in Chrony configuration.

Example:

```text
server ntp1.example.com iburst
```

The `iburst` option helps Chrony perform the **initial synchronization faster**.

When Chrony first communicates with the NTP source, it sends a burst of measurements instead of waiting for a long time between the initial measurements.

This helps Chrony reach the correct time more quickly.

Remember:

> **`iburst` is mainly useful for faster initial synchronization.**

It does not mean that Chrony continuously sends a large number of requests.

---

# Apply the Configuration

After changing:

```text
/etc/chrony.conf
```

restart the Chrony service:

```bash
systemctl restart chronyd
```

Then check the service:

```bash
systemctl status chronyd
```

Make sure the service is running successfully.

---

# Verify NTP Synchronization

After configuring Chrony, we need to check whether the Linux server is actually communicating with the configured NTP sources.

Use:

```bash
chronyc sources -v
```

Example:

```text
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* ntp1.example.com             2     6   377    20   +123us
^+ ntp2.example.com             2     6   377    30    -45us
^- ntp3.example.com             3     6   377    25   +210us
```

The symbols are important:

```text
* → Currently selected source
+ → Good / usable source
- → Source not currently selected
```

For example:

```text
^* ntp1.example.com
```

The `*` tells us that Chrony has currently selected this source for synchronization.

If we see:

```text
^+ ntp2.example.com
```

the source is usable, but it is not currently selected.

---

# Practical NTP Setup

Suppose we have a Linux server and want to configure three NTP servers.

## Install Chrony

```bash
dnf install chrony -y
```

## Enable and Start Chrony

```bash
systemctl enable --now chronyd
```

## Open the Configuration File

```bash
vim /etc/chrony.conf
```

Add:

```text
server ntp1.example.com iburst
server ntp2.example.com iburst
server ntp3.example.com iburst
```

Save the file.

## Restart Chrony

```bash
systemctl restart chronyd
```

## Check the Service

```bash
systemctl status chronyd
```

The service should be running.

## Check NTP Sources

```bash
chronyc sources -v
```

Look for a source marked with:

```text
*
```

For example:

```text
^* ntp1.example.com
```

This indicates that Chrony has selected that source for synchronization.

---

