# Linux Services and Daemons

In Linux, many programs need to keep running in the background.

For example:

* SSH
* Web Server
* Database
* Time Synchronization

These background programs usually work continuously without direct user interaction.

To understand these programs, we mainly need to understand two terms:

* **Daemon**
* **Service**

---

# 1. What is a Daemon?

A **daemon is an actual background program that performs a particular task in Linux.**

For example:

```bash
sshd
```

`sshd` is the SSH daemon.

Its job is to:

> Listen for incoming SSH connection requests and handle SSH connections.

So simply remember:

```text
sshd = Actual background program
```

Some common daemons are:

```text
sshd
httpd
chronyd
```

Traditionally, many daemon names end with **`d`**.

---

# 2. What is a Service?

Now we have a daemon:

```text
sshd
```

But we also need to manage this daemon.

For example, we may need to:

* Start SSH
* Stop SSH
* Restart SSH
* Reload SSH
* Start SSH automatically during boot

For these management tasks, Linux uses the concept of a **service**.

The SSH service is:

```text
sshd.service
```

Simply remember:

```text
sshd
   ↓
Actual background program

sshd.service
   ↓
Used to manage sshd
```

So:

> **The daemon does the actual work, and the service is used to manage that daemon.**

---

# 3. What is systemd?

Now the question is:

**Who manages the service?**

Linux uses **systemd** for this.

In simple words:

> **systemd is the service manager in Linux.**

When we start, stop, restart, or reload a service, we are actually managing that service **through systemd**.

For example:

```text
systemd
   ↓
sshd.service
   ↓
sshd
```

So remember:

```text
systemd → Manages the service
Service → Manages the daemon
Daemon  → Performs the actual background work
```

---

# 4. What is systemctl?

We use a command called:

```bash
systemctl
```

to communicate with systemd.

You can think of `systemctl` as a **command used to manage systemd services**.

For example:

```bash
systemctl status sshd
```

This means:

> Ask systemd to show the current status of the SSH service.

The simple flow is:

```text
systemctl
    ↓
systemd
    ↓
sshd.service
    ↓
sshd
```

---

# 5. Check Service Status

To check the current status of the SSH service:

```bash
systemctl status sshd
```

If SSH is running, you may see:

```text
Active: active (running)
```

This means:

> The SSH service is currently running.

If the service is stopped, you may see:

```text
Active: inactive (dead)
```

This means:

> The SSH service is currently not running.

So, before managing a service, we can first check its status:

```bash
systemctl status sshd
```

---

# 6. Start a Service

If the `sshd` service is stopped and we want to start it:

```bash
systemctl start sshd
```

This means:

> Start the SSH service now.

After starting it, we can check the status:

```bash
systemctl status sshd
```

Normally, we should see:

```text
active (running)
```

---

# 7. Stop a Service

If we want to stop the SSH service:

```bash
systemctl stop sshd
```

This means:

> Stop the SSH service now.

Then check the status:

```bash
systemctl status sshd
```

Normally, we should see:

```text
inactive (dead)
```

### Important

If the SSH service is stopped, the server will not accept **new SSH connections**.

So be careful when stopping SSH on a remote server.

---

# 8. Restart a Service

If we want to completely stop and start a service again:

```bash
systemctl restart sshd
```

The simple process is:

```text
Running Service
      ↓
    Stop
      ↓
    Start
```

Restart is useful when we need to completely restart a service.

For example:

```bash
systemctl restart httpd
```

This will restart the Apache service.

---

# 9. Reload a Service

`reload` and `restart` are different.

Reload means:

> **Ask the running service to read its configuration again without completely stopping the service.**

For example:

```bash
systemctl reload httpd
```

Simple difference:

```text
restart

Service
   ↓
Stop
   ↓
Start again
```

But:

```text
reload

Service keeps running
   ↓
Reads configuration again
```

So:

```text
restart = Stop + Start

reload = Read configuration again
```

If a service supports reload, it can be useful when configuration changes are made and we do not want to completely restart the service.

---

# 10. Start a Service Automatically During Boot

Now consider this situation.

We start a service using:

```bash
systemctl start httpd
```

The service starts **right now**.

But after a server reboot, the service may not automatically start.

If we want the service to start automatically during boot, use:

```bash
systemctl enable httpd
```

This means:

> Start the `httpd` service automatically when the server boots.

So remember:

```text
start
   ↓
Start the service now

enable
   ↓
Start the service automatically during boot
```

---

# 11. Disable a Service

If we do not want a service to start automatically during boot:

```bash
systemctl disable httpd
```

This means:

> Do not automatically start `httpd` during boot.

Remember:

```text
disable ≠ stop
```

`disable` controls **automatic startup during boot**.

If the service is currently running and we want to stop it now:

```bash
systemctl stop httpd
```

---

# 12. Start and Enable Together

Sometimes we want to do two things:

1. Start the service now
2. Enable the service for future boots

We can run two commands:

```bash
systemctl start httpd
systemctl enable httpd
```

Or we can do both in one command:

```bash
systemctl enable --now httpd
```

This means:

> **Start the service now and also enable it for automatic startup during boot.**

---

# 13. Important Commands — Quick Revision

### Check status

```bash
systemctl status sshd
```

### Start a service

```bash
systemctl start sshd
```

### Stop a service

```bash
systemctl stop sshd
```

### Restart a service

```bash
systemctl restart sshd
```

### Reload a service

```bash
systemctl reload sshd
```

### Enable a service at boot

```bash
systemctl enable sshd
```

### Disable a service at boot

```bash
systemctl disable sshd
```

### Start now + Enable at boot

```bash
systemctl enable --now sshd
```

---

# 14. Most Important Concept

Remember the complete flow:

```text
systemctl
    ↓
systemd
    ↓
Service
    ↓
Daemon
```

For SSH:

```text
systemctl
    ↓
systemd
    ↓
sshd.service
    ↓
sshd
```

And remember the commands:

```text
status
→ Check the service status

start
→ Start the service now

stop
→ Stop the service now

restart
→ Stop and start the service again

reload
→ Read the configuration again

enable
→ Start automatically during boot

disable
→ Do not start automatically during boot

enable --now
→ Start now + Enable for boot
```

### In one sentence:

> **We use `systemctl` to communicate with `systemd`, and through systemd we can start, stop, restart, reload, enable, and disable Linux services.**
