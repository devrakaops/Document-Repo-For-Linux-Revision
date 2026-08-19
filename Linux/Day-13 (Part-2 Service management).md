# Part-2 (Service Management)
Linux Remote Access and Security Operations

---

# 1. Introduction to Remote Access

In Linux administration, we often need to manage a server from another machine.

For example:

* We are sitting on our laptop.
* The Linux server is running in a data center or cloud.
* We need to log in to that server.
* We may need to copy files from one server to another.
* We may also need to transfer large amounts of data regularly.

For these requirements, Linux provides tools such as:

* **SSH** → Remote login
* **SCP** → Secure file copying
* **Rsync** → Smart and efficient file synchronization

Before understanding these commands, we should understand **why SSH is required for secure communication**.

---

# 2. Telnet vs SSH

Earlier, administrators commonly used **Telnet** for remote server access.

The basic problem with Telnet is that it sends communication in **plain text**.

For example, suppose a user connects to a server using Telnet:

```text
Client ------------------------> Server
       Username: gaurav
       Password: 123456
```

If someone is able to capture the network traffic, the information can potentially be read.

This creates a major security problem.

Therefore, Telnet is considered an **insecure remote-access protocol**.

SSH was introduced to provide secure remote access.

```text
Telnet
   ↓
Plain-text communication
   ↓
Insecure

SSH
   ↓
Encrypted communication
   ↓
Secure
```

**SSH = Secure Shell**

SSH provides secure communication between the client and the remote server.

---

# 3. Encryption

The main reason SSH is secure is **encryption**.

Encryption means converting readable information into an unreadable format.

For example:

```text
Original Data
     ↓
Encryption
     ↓
Encrypted Data
```

The receiver uses the appropriate key to convert the encrypted information back into readable information.

There are two important types of encryption that we need to understand:

1. Symmetric Encryption
2. Asymmetric Encryption

---

# 4. Symmetric Encryption

In symmetric encryption, the **same key** is used for encryption and decryption.

```text
             Same Key
                |
                ↓
Data → Encryption → Encrypted Data
                         |
                         ↓
                    Decryption
                         |
                         ↓
                        Data
```

The main problem is **key sharing**.

Suppose Gaurav wants to securely communicate with Ravi.

Gaurav has a secret key:

```text
Secret Key = ABC123
```

Ravi also needs the same key to decrypt the communication.

So Gaurav has to somehow securely provide this key to Ravi.

This creates a problem:

> How can we securely transfer the secret key before secure communication has even started?

This is one of the major limitations of symmetric encryption.

---

# 5. Asymmetric Encryption

Asymmetric encryption solves the key-sharing problem by using **two different keys**.

These are:

* **Public Key**
* **Private Key**

The keys are mathematically related, but they are used differently.

```text
Asymmetric Encryption

        User
         |
    ┌────┴────┐
    ↓         ↓
Public Key  Private Key
```

### Public Key

The public key can be shared with others.

It does not need to be kept secret.

### Private Key

The private key must be kept secret.

It should never be shared with another person.

---

# 6. Gaurav and Ravi Example

Suppose Gaurav wants to securely communicate with Ravi.

Ravi has:

```text
Public Key
Private Key
```

Ravi can freely give his **public key** to Gaurav.

```text
Ravi
 ├── Public Key  → Give to Gaurav
 └── Private Key → Keep secret
```

Gaurav can use Ravi's public key for the required cryptographic operation.

The important concept to remember is:

> Public keys can be shared, but private keys must remain secret.

SSH uses cryptographic techniques involving public and private keys to establish secure communication.

---

# 7. What is SSH?

**SSH stands for Secure Shell.**

SSH is used to securely connect to a remote Linux server.

Basic syntax:

```bash
ssh username@IP_Address
```

Example:

```bash
ssh root@192.168.1.20
```

Here:

```text
ssh       → SSH command
root      → Remote username
192.168.1.20 → Remote server IP address
```

When the connection is successful, we get a shell of the remote server.

For example:

```bash
[root@client ~]# ssh root@192.168.1.20
```

After authentication:

```bash
[root@server ~]#
```

Now we are working on the remote server.

---

# 8. SSH Requirements

Before using SSH, some basic requirements must be satisfied.

## Requirement 1: SSH Package

The OpenSSH software should be installed on the server.

We can check the installed SSH package using:

```bash
rpm -qa | grep openssh
```

Depending on the Linux distribution and package setup, OpenSSH-related packages may include:

```text
openssh
openssh-server
openssh-clients
```

The **SSH server** component is required on the machine that will accept SSH connections.

---

# 9. SSH Port

By default, SSH uses:

```text
Port 22
```

Therefore, the SSH server should be listening on port 22 unless the administrator has configured another port.

We can check listening ports using:

```bash
netstat -tulpn
```

or:

```bash
ss -pulpn
```

We may see something similar to:

```text
tcp    0    0    0.0.0.0:22    0.0.0.0:*    LISTEN
```

This indicates that a service is listening on port 22.

---

# 10. SSH Service

The SSH server is normally provided by the `sshd` service.

We can check its status using:

```bash
systemctl status sshd
```

If it is not running:

```bash
systemctl start sshd
```

To make it start automatically after reboot:

```bash
systemctl enable sshd
```

---

# 11. SSH Connection to a Remote Server

The general syntax is:

```bash
ssh username@IP_Address
```

Example:

```bash
ssh gaurav@192.168.1.20
```

The SSH client connects to the remote server.

The remote server verifies the user and authentication information.

If authentication succeeds, the user gets access to the remote shell.

---

# 12. SSH to Localhost

SSH can also be tested on the same machine.

For example:

```bash
ssh root@localhost
```

or:

```bash
ssh root@127.0.0.1
```

Here, the connection is being made to the same machine.

This is useful when we want to test whether the local SSH service is working correctly.

---

# 13. SCP – Secure Copy

SSH is mainly used for remote login.

But what if we want to **copy files between Linux systems securely?**

For this purpose, we can use:

**SCP = Secure Copy**

SCP works over SSH.

Therefore, SCP provides secure file transfer between systems.

Basic syntax:

```bash
scp source destination
```

For example:

```bash
scp file.txt root@192.168.1.20:/tmp/
```

This means:

```text
Local file
   ↓
file.txt
   ↓
Remote server
192.168.1.20
   ↓
/tmp/
```

---

# 14. Understanding SCP Username and IP

When using SCP, we normally specify:

```text
username@IP_Address
```

For example:

```bash
root@192.168.1.20
```

Think of it like sending something to a person's address.

```text
root       → Who?
192.168.1.20 → Where?
```

So:

```text
username → identifies the user
IP       → identifies the remote machine
```

This makes it easy to understand the destination.

---

# 15. Copy File From Local to Remote

Suppose we have:

```text
backup.txt
```

on the local machine.

We want to copy it to:

```text
192.168.1.20:/backup/
```

Command:

```bash
scp backup.txt root@192.168.1.20:/backup/
```

The file will be transferred securely to the remote server.

---

# 16. Copy File From Remote to Local

SCP can also copy files in the opposite direction.

For example:

```bash
scp root@192.168.1.20:/backup/backup.txt /tmp/
```

Here:

```text
Remote server
      ↓
backup.txt
      ↓
Local machine
      ↓
/tmp/
```

---

# 17. Copying Directories Using SCP

By default, SCP works with files.

If we want to copy a complete directory, we use:

```bash
-r
```

The `-r` option means **recursive**.

Example:

```bash
scp -r /data root@192.168.1.20:/backup/
```

This copies the complete `/data` directory and its contents to the remote server.

---

# 18. Limitations of SCP

SCP is useful for straightforward file transfers.

However, suppose we are transferring a very large file.

The transfer reaches:

```text
50%
```

Then the network connection fails.

If we start the SCP transfer again, SCP may need to transfer the file again from the beginning.

For example:

```text
Transfer:
0% → 25% → 50% → Network Failure
                         ↓
                    Start again
                         ↓
0% → 25% → 50% → 100%
```

This can waste:

* Time
* Bandwidth
* Network resources

This is where **Rsync** becomes very useful.

---

# 19. Rsync

**Rsync** stands for **Remote Synchronization**.

It is used to synchronize files and directories between systems.

The important feature of Rsync is that it can transfer **only the required differences** instead of transferring everything again.

Therefore, we can think of Rsync as:

> **Smart Synchronization**

---

# 20. Rsync and Delta Transfer

Suppose we are transferring a large file.

The transfer reaches:

```text
51%
```

Then the network connection fails.

When we use Rsync again, it can determine what data is already present and what data is still required.

Conceptually:

```text
First transfer:

0% ───────────────→ 51%
                         X
                   Network failure
```

When the transfer is started again:

```text
Already available → 51%
Required data     → Remaining portion
```

So Rsync does not unnecessarily transfer everything again.

This saves bandwidth and time.

---

# 21. Why Rsync is Called Smart Synchronization

Suppose the source contains:

```text
file1
file2
file3
file4
```

The destination already contains:

```text
file1
file2
file3
```

Only `file4` needs to be transferred.

Rsync can compare the source and destination and transfer the required changes.

This is very useful when working with:

* Large files
* Backup systems
* Server synchronization
* Repeated data transfers
* Remote backups

---

# 22. Basic Rsync Command

Basic syntax:

```bash
rsync source destination
```

Example:

```bash
rsync backup.tar root@192.168.1.20:/backup/
```

For directory synchronization, commonly:

```bash
rsync -av /data/ root@192.168.1.20:/backup/
```

Here:

```text
-a → archive mode
-v → verbose mode
```

---

# 23. SCP vs Rsync

Both SCP and Rsync can transfer files securely.

But their approach is different.

| SCP                                         | Rsync                                  |
| ------------------------------------------- | -------------------------------------- |
| Secure file copy                            | File synchronization                   |
| Simple file transfer                        | Smart synchronization                  |
| Useful for straightforward copying          | Useful for repeated synchronization    |
| Can restart a large transfer unnecessarily  | Can transfer only required differences |
| Less efficient for repeated large transfers | More efficient for repeated transfers  |

A simple way to remember:

```text
SCP   → Copy
Rsync → Synchronize
```

---

# 24. Password-Based Authentication

Normally, when we connect through SSH:

```bash
ssh root@192.168.1.20
```

the server may ask for a password.

Example:

```text
root@192.168.1.20's password:
```

The administrator enters the password and gets access.

This is fine for manual login.

But consider automation.

---

# 25. Why Passwordless SSH is Required

Suppose Jenkins needs to connect to a Linux server automatically.

The process may look like:

```text
Jenkins
   ↓
SSH
   ↓
Linux Server
```

If SSH asks:

```text
Enter password:
```

Jenkins cannot normally stop and manually enter the password.

Therefore, automated systems require **passwordless/key-based SSH authentication**.

Examples include:

* CI/CD pipelines
* Jenkins
* Automated scripts
* Configuration management
* Ansible
* Automated server operations

The basic idea is:

```text
Automation
    ↓
SSH
    ↓
Authentication using SSH keys
    ↓
Remote Server
```

---

# 26. SSH Key-Based Authentication

For key-based authentication, we generate a key pair.

The key pair contains:

```text
Private Key
Public Key
```

For example:

```text
~/.ssh/
├── id_rsa
└── id_rsa.pub
```

or with newer key types, names such as:

```text
id_ed25519
id_ed25519.pub
```

The important rule is:

```text
Private Key → Keep Secret
Public Key  → Can be copied to server
```

---

# 27. Generate SSH Keys

We can generate an SSH key pair using:

```bash
ssh-keygen
```

Example:

```bash
ssh-keygen
```

The command generates the key pair.

Depending on the selected key type, the files may be stored under:

```bash
~/.ssh/
```

For example:

```text
id_rsa
id_rsa.pub
```

Here:

```text
id_rsa     → Private key
id_rsa.pub → Public key
```

**Never share the private key.**

---

# 28. Copy Public Key to Remote Server

After generating the key pair, we need to place the public key on the remote server.

The commonly used command is:

```bash
ssh-copy-id username@IP_Address
```

Example:

```bash
ssh-copy-id root@192.168.1.20
```

The public key is added to the remote user's SSH authorized keys.

Conceptually:

```text
Client
 ├── Private Key
 └── Public Key
          |
          ↓
Remote Server
 └── Authorized Public Key
```

---

# 29. Passwordless SSH Flow

The complete process is:

```text
Step 1
Generate SSH key pair

        ssh-keygen
             ↓
     Public + Private Key

Step 2
Copy public key

        ssh-copy-id
             ↓
     Remote Server

Step 3
Connect using SSH

        ssh user@server
             ↓
      Key-based authentication
             ↓
          Login
```

After successful configuration, SSH can authenticate using the key instead of asking for the user's password.

---

# 30. Changing the Default SSH Port

The default SSH port is:

```text
22
```

An administrator may change it to another port.

For example:

```text
2222
```

The SSH server configuration is generally stored in:

```bash
/etc/ssh/sshd_config
```

We can configure:

```text
Port 2222
```

After changing the configuration, the SSH service needs to be restarted or reloaded as appropriate.

For example:

```bash
systemctl restart sshd
```

Then we can connect using:

```bash
ssh -p 2222 username@IP_Address
```

Here:

```text
-p 2222
```

specifies the custom SSH port.

---

# 31. Why Change SSH Port?

Port 22 is the standard and well-known SSH port.

Internet-facing servers may receive automated scanning and login attempts against port 22.

Changing the SSH port can reduce some automated scanning noise.

However, remember:

> Changing the SSH port is not a replacement for proper SSH security.

Authentication, firewall rules, key-based login, access control, and other security controls are still important.

---

# 32. SELinux and SSH Port Changes

When changing SSH configuration, SELinux can become relevant.

SELinux can restrict which ports a service is allowed to use.

During classroom/practical exercises, SELinux may sometimes be temporarily disabled to avoid service startup or access issues.

Temporary permissive mode:

```bash
setenforce 0
```

This change is temporary and normally lasts until the next reboot or until SELinux mode is changed again.

---

# 33. Permanent SELinux Configuration

The main SELinux configuration file is:

```bash
/etc/selinux/config
```

For example, an administrator may configure:

```text
SELINUX=disabled
```

After making such a configuration change, a reboot may be required.

For example:

```bash
init 6
```

`init 6` is used to reboot the system.

**Important:** Disabling SELinux permanently reduces a security layer. In production environments, it should not be disabled just to make SSH configuration easier. The preferred approach is to configure SELinux correctly for the required SSH port.

---

# 34. Direct Root Login

By default, SSH security configuration may restrict direct root login depending on the system configuration.

The relevant configuration file is:

```bash
/etc/ssh/sshd_config
```

A related configuration parameter is:

```text
PermitRootLogin
```

For example:

```text
PermitRootLogin no
```

This prevents direct root login through SSH.

The idea is:

```text
User
  ↓
SSH Login
  ↓
Normal Account
  ↓
sudo
  ↓
Administrative Privileges
```

This is generally safer than allowing direct root login from remote systems.

---

# 35. AllowUsers

SSH also provides access control through:

```text
AllowUsers
```

This parameter can be used to define which users are allowed to connect through SSH.

Example:

```text
AllowUsers gaurav ravi
```

This means SSH access is allowed for the specified users.

Conceptually:

```text
SSH Users
   |
   ├── Gaurav → Allowed
   ├── Ravi   → Allowed
   └── Other  → Not allowed
```

This provides a simple SSH user whitelist.

---

# 36. DenyUsers

Another option is:

```text
DenyUsers
```

This can be used to block specific users from SSH access.

Example:

```text
DenyUsers testuser
```

Conceptually:

```text
SSH Users
   |
   ├── Gaurav   → Allowed
   ├── Ravi     → Allowed
   └── testuser → Denied
```

This provides a simple SSH user blacklist.

---

# 37. AllowUsers vs DenyUsers

The basic difference is:

```text
AllowUsers
    ↓
Define who is allowed

DenyUsers
    ↓
Define who is blocked
```

Both are configured in:

```bash
/etc/ssh/sshd_config
```

After modifying the SSH configuration, the SSH service should be reloaded or restarted as required.

For example:

```bash
systemctl restart sshd
```

---

# 38. Important SSH Configuration File

The most important SSH server configuration file discussed in this session is:

```bash
/etc/ssh/sshd_config
```

Important parameters include:

```text
Port
PermitRootLogin
AllowUsers
DenyUsers
```

Whenever this file is modified, always verify the configuration carefully before restarting SSH.

A configuration mistake can prevent SSH access to the server.

---

# 39. Complete Remote Access Flow

The complete concept covered in this session can be remembered like this:

```text
                    Linux Remote Operations
                            |
        ┌───────────────────┼───────────────────┐
        ↓                   ↓                   ↓
       SSH                 SCP                Rsync
        |                   |                   |
   Remote Login        Secure Copy        Synchronization
        |                   |                   |
        ↓                   ↓                   ↓
 Authentication        File Transfer       Smart Transfer
        |
   ┌────┴─────┐
   ↓          ↓
Password     SSH Keys
             |
       Passwordless
       Authentication
```

---

# 40. Important Commands – Quick Revision

### Check OpenSSH packages

```bash
rpm -qa | grep openssh
```

### Check listening ports

```bash
netstat -tulpn
```

or:

```bash
ss -pulpn
```

### Check SSH service

```bash
systemctl status sshd
```

### Start SSH service

```bash
systemctl start sshd
```

### Enable SSH service

```bash
systemctl enable sshd
```

### SSH connection

```bash
ssh username@IP_Address
```

### SSH localhost

```bash
ssh root@localhost
```

### SCP file to remote server

```bash
scp file.txt root@192.168.1.20:/tmp/
```

### SCP directory

```bash
scp -r /data root@192.168.1.20:/backup/
```

### Rsync

```bash
rsync -av /data/ root@192.168.1.20:/backup/
```

### Generate SSH keys

```bash
ssh-keygen
```

### Copy public key

```bash
ssh-copy-id username@IP_Address
```

### Temporary SELinux permissive mode

```bash
setenforce 0
```

### SELinux configuration

```bash
/etc/selinux/config
```

### SSH configuration

```bash
/etc/ssh/sshd_config
```

### Restart SSH

```bash
systemctl restart sshd
```

### Reboot system

```bash
init 6
```

---

# 41. Final Revision

The most important concepts from Day 23–24 are:

**SSH**

Used for secure remote login.

```bash
ssh username@IP_Address
```

**SCP**

Used for secure file and directory copying.

```bash
scp file.txt username@IP_Address:/path/
```

For directories:

```bash
scp -r directory username@IP_Address:/path/
```

**Rsync**

Used for efficient synchronization and can avoid unnecessary retransmission of data.

```bash
rsync -av source destination
```

**SSH Key-Based Authentication**

Used for passwordless and automated SSH access.

```bash
ssh-keygen
ssh-copy-id username@IP_Address
```

**SSH Security**

Important configuration file:

```bash
/etc/ssh/sshd_config
```

Important parameters:

```text
Port
PermitRootLogin
AllowUsers
DenyUsers
```

The overall idea is simple:

> **SSH provides secure remote access, SCP provides secure copying, and Rsync provides efficient synchronization.**
