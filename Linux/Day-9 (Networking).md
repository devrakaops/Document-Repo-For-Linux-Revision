# Linux Networking Basics (Only Required for RHCSA Practical)

> **Goal of this session**
>
> This session is only for learning the basic networking concepts required to configure a Linux system.

---

# What is a Network?

A **Network** is a connection between two or more devices so that they can communicate and share data.

Examples:

* Laptop to Laptop
* Laptop to Printer
* Laptop to Server
* Laptop to Internet

When devices are connected and can exchange data, they are part of a **Network**.

---

# How Does a Computer Get Internet?

A computer does not create the Internet by itself.

It receives Internet from an **Internet Service Provider (ISP)**.

The Internet can reach our computer in two common ways.

### Method 1: Through a Wi-Fi Router

```
Internet
    │
    │
ISP
    │
    │
Wi-Fi Router
    │
    │
Laptop
```

The router receives Internet from the ISP and shares it with our computer.

---

### Method 2: Through Mobile Network

```
Internet
    │
Mobile Tower
    │
Mobile Phone
```

The mobile phone receives Internet from the mobile tower.

---

# What is an IP Address?

Every device connected to a network needs its own unique address.

This address is called an **IP Address**.

Just like every house has its own address, every computer on a network has its own IP address.

Example:

```
Laptop      → 192.168.1.10

Printer     → 192.168.1.20

Server      → 192.168.1.30
```

Without an IP address, devices cannot communicate with each other.

---

# What is a Subnet Mask?

A **Subnet Mask** tells the computer which part of the IP address belongs to the network and which part belongs to the device (host).

In most practical labs, we use:

```
255.255.255.0
```

For this course, you only need to remember this subnet mask.

---

# What is a Gateway?

A **Gateway** is the path that allows a computer to communicate with other networks or the Internet.

In most networks, the **Router IP Address** is used as the Gateway.

Example:

```
Laptop
   │
   ▼
Router (Gateway)
   │
Internet
```

Example Gateway:

```
192.168.1.254
```

Whenever the computer wants to access Google, YouTube, or any other website, it first sends the request to the Gateway (Router).

---

# What is DNS?

DNS stands for **Domain Name System**.

Humans remember website names, but computers understand IP addresses.

DNS converts a **Domain Name** into an **IP Address**.

Example:

```
google.com
        │
        ▼
142.xx.xx.xx
```

Without DNS, we would need to remember the IP address of every website.

---

# What is a Hostname?

A **Hostname** is the name of a computer.

Examples:

```
server1

webserver

database

primary.net1.example.com
```

Hostname helps us identify systems easily.

---

# DHCP

**DHCP (Dynamic Host Configuration Protocol)** automatically provides network settings to a computer.

It automatically assigns:

* IP Address
* Subnet Mask
* Gateway
* DNS Server

Example:

```
Laptop
      │
      ▼
Router (DHCP Server)
      │
Automatically provides IP Address
```

No manual configuration is required.

---

# Static IP

A **Static IP Address** is configured manually by the administrator.

We manually enter:

* IP Address
* Subnet Mask
* Gateway
* DNS Server

Servers usually use **Static IP** because the IP address should not change.

Example:

```
IP Address    : 192.168.1.1

Subnet Mask   : 255.255.255.0

Gateway       : 192.168.1.254

DNS Server    : 192.168.1.10
```

---

# Methods to Configure Network in Linux

Linux provides three common methods to configure network settings.

---

## 1. nmcli (Command Line Method)

`nmcli` stands for **Network Manager Command Line Interface**.

It is used to configure network settings from the Linux terminal.

Example:

```bash
nmcli connection show
nmcli connection down <connection-name>
nmcli connection up <connection-name>
nmcli connection modify ens160 ipv4.addresses 192.168.X.X/24 ipv4.gateway 192.168.X.X ipv4.dns 8.8.X.X ipv4.method manual

```
<img width="983" height="88" alt="image" src="https://github.com/user-attachments/assets/c11d7a84-4d59-48cd-a173-42a9cd0d2990" />

---

## 2. nmtui (Text User Interface)

`nmtui` stands for **Network Manager Text User Interface**.

It provides a menu-based interface inside the terminal.

Example:

```bash
nmtui
```
<img width="1359" height="459" alt="image" src="https://github.com/user-attachments/assets/1ac7dea6-f73a-49ca-820c-70317a80c515" />


You can configure:

* IP Address
* Gateway
* DNS
* Hostname

without remembering long commands.

---

## 3. GUI Method (Graphical User Interface)

In the GUI method, network settings are changed using the graphical desktop.

Typical path:

```
Top Right Corner
      ↓
Settings
      ↓
Network
      ↓
IPv4 Settings
```

Here you can configure:

* Static IP
* Gateway
* DNS
* DHCP

using the mouse.

---

# Useful Networking Commands

## 1. Check IP Address

```bash
ifconfig
```

or

```bash
ip a
```

Displays all network interfaces and their IP addresses.

---

## 2. Check Routing Table

```bash
ip route
```

or

```bash
route -n
```

Displays the routing table and the default gateway.

---

## 3. Check Hostname

```bash
hostname
```

Displays the current hostname.

---

## 4. Change Hostname

```bash
hostnamectl set-hostname primary.net1.example.com
```

Changes the system hostname.

---

## 5. Test Network Connectivity

```bash
ping 8.8.8.8
```

Tests whether the system can reach another IP address.

Example:

```bash
ping google.com
```

Tests both network connectivity and DNS resolution.

---

## 6. Check DNS Resolution

```bash
nslookup google.com
```
<img width="1328" height="332" alt="image" src="https://github.com/user-attachments/assets/c129d071-aa11-4a8b-aa56-bedd94997220" />


Displays the IP address of a domain name using the configured DNS server.

---

## 7. Download a File or page over the internet

```bash
wget https://example.com/file.txt
```

wget used to Downloads a file from a website.

---

## 8. Access a Website from Terminal

```bash
curl https://example.com
```

Retrieves content from a website or API. It is commonly used to test web servers and REST APIs.

---


Lists all available network connections managed by NetworkManager.

---

# Example Practical Question

Configure the Primary Virtual Machine with the following details.

| Parameter   | Value                    |
| ----------- | ------------------------ |
| IP Address  | 192.168.10.12            |
| Subnet Mask | 255.255.255.0            |
| Gateway     | 192.168.102              |
| DNS Server  | 8.8.8.8                  |
| Hostname    | primary.net1.example.com |

---

# Quick Revision

| Parameter       | Purpose                                                    |
| --------------- | ---------------------------------------------------------- |
| **IP Address**  | Unique address of the computer on the network.             |
| **Subnet Mask** | Identifies the network and host portion of the IP address. |
| **Gateway**     | Router IP used to reach other networks and the Internet.   |
| **DNS Server**  | Converts domain names into IP addresses.                   |
| **Hostname**    | Name of the computer.                                      |
| **DHCP**        | Automatically assigns network settings.                    |
| **Static IP**   | Network settings are configured manually.                  |
| **nmcli**       | Command-line tool for network configuration.               |
| **nmtui**       | Text-based interface for network configuration.            |
| **GUI**         | Graphical method to configure network settings.            |

---

# Summary

To configure a Linux system for networking, we mainly need five settings:

* IP Address
* Subnet Mask
* Gateway
* DNS Server
* Hostname

A system can receive these settings automatically using **DHCP** or manually using a **Static IP** configuration. In Linux, network settings can be configured using **nmcli (Command Line)**, **nmtui (Text User Interface)**, or the **GUI (Graphical Interface)**.

This knowledge is enough to understand and solve basic Linux network configuration tasks such as configuring a server with a static IP address in RHCSA and day-to-day Linux administration.
