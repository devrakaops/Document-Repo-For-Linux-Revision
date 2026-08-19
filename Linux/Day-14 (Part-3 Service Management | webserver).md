# Linux Service Management – Web Server Setup

## 1. What is a Web Server?

A **Web Server** is a machine/service that provides web pages or web applications to clients over a network.

For example:

```text
Client Browser
      |
      | HTTP Request
      v
Linux Web Server
      |
      | Port 80
      v
Web Page / Application
```

If we want to convert our Linux machine into a web server, we generally need to:

1. Check network/internet connectivity.
2. Check configured YUM repositories.
3. Decide which web server software we need.
4. Install the required package.
5. Start and enable the service.
6. Allow the required port/service in the firewall.
7. Configure the web server.
8. Test access using the server IP address.

---

# 2. Check Internet / Network Connectivity

Before installing any package, first make sure that the machine has network connectivity.

For example:

```bash
ping google.com
```

If you receive replies, the machine is able to communicate with the internet.

Example:

```text
64 bytes from ...
64 bytes from ...
```

If ping fails, first troubleshoot the network before trying to install packages.

> Note: Ping failure does not always mean that internet is completely unavailable because ICMP can be blocked. But for a basic lab check, `ping` is commonly used.

---

# 3. Check YUM Repositories

Packages such as `httpd` are normally installed from configured repositories.

First check the available repositories:

```bash
yum repolist all
```

This shows the repositories configured on the system.

Example:

```text
repo id              repo name              status
appstream            AppStream              enabled
baseos               BaseOS                 enabled
```

We can also check repository information using:

```bash
yum repoinfo
```

The important point is:

**Before installing a package, make sure the required YUM repository is available and enabled.**

---

# 4. Decide Which Web Server We Need

Linux can use different web server software.

Common examples are:

* Apache HTTP Server
* NGINX
* Other web server software

For this documentation, we will use **Apache HTTP Server**.

On RHEL-based systems, the Apache HTTP Server package is called:

```text
httpd
```

So if we decide to use Apache, we need to install the `httpd` package.

---

# 5. Install Apache HTTP Server

Install the package using YUM:

```bash
yum install httpd
```

After installation, verify that the package is installed:

```bash
rpm -q httpd
```

You can also check the package information:

```bash
yum info httpd
```

---

# 6. Start the Web Server Service

After installing the package, start the Apache service.

```bash
systemctl start httpd.service
```

To restart the service:

```bash
systemctl restart httpd.service
```

To check the current status:

```bash
systemctl status httpd.service
```

If the service is running, we should see:

```text
Active: active (running)
```

### Start vs Restart

`start` means:

> Start the service if it is currently stopped.

`restart` means:

> Stop and start the service again.

Normally, immediately after installation, we use:

```bash
systemctl start httpd
```

When we modify configuration files, we may use:

```bash
systemctl restart httpd
```

---

# 7. Enable the Service at Boot

Starting the service does not automatically mean it will start after a reboot.

To make Apache start automatically when the system boots:

```bash
systemctl enable httpd
```

We can do both operations together:

```bash
systemctl enable --now httpd
```

This means:

* Start the service now.
* Enable the service for future boot.

Check:

```bash
systemctl status httpd
```

---

# 8. Check the Web Server Port

Apache normally listens on:

```text
Port 80
```

Port 80 is the default port for HTTP.

We can check listening ports using:

```bash
ss -lntp
```

Or specifically check port 80:

```bash
ss -lntp | grep :80
```

We should see Apache/httpd listening on port 80.

Example:

```text
LISTEN ... :80 ... httpd
```

---

# 9. Configure the Firewall

Even if Apache is running and listening on port 80, the firewall may block incoming traffic.

Therefore, we need to allow HTTP traffic through the firewall.

## Method 1: Allow Port 80

```bash
firewall-cmd --permanent --add-port=80/tcp
```

Here:

* `--permanent` → save the firewall rule permanently.
* `--add-port=80/tcp` → allow TCP traffic on port 80.

After making a permanent firewall change, reload the firewall:

```bash
firewall-cmd --reload
```

Check the configuration:

```bash
firewall-cmd --list-all
```

We should see port 80 in the output.

---

# 10. Allow HTTP Service Instead of Port

Instead of manually adding port 80, we can use the predefined HTTP firewall service.

```bash
firewall-cmd --permanent --add-service=http
```

Then reload:

```bash
firewall-cmd --reload
```

Check:

```bash
firewall-cmd --list-all
```

### Port vs Service

Both approaches allow HTTP traffic.

Using port:

```bash
firewall-cmd --permanent --add-port=80/tcp
```

Using predefined service:

```bash
firewall-cmd --permanent --add-service=http
```

For standard services, using the predefined service is generally cleaner.

---

# 11. Important Apache Configuration File

The main Apache configuration file is:

```text
/etc/httpd/conf/httpd.conf
```

This is one of the most important files when working with Apache.

We can open it using:

```bash
vim /etc/httpd/conf/httpd.conf
```

or:

```bash
vi /etc/httpd/conf/httpd.conf
```

---

# 12. Important Apache Directories and Files

Some important Apache locations are:

```text
/etc/httpd/
```

Main Apache configuration directory.

```text
/etc/httpd/conf/httpd.conf
```

Main Apache configuration file.

```text
/var/www/html/
```

Default document root.

```text
/var/log/httpd/
```

Apache log directory.

The important locations to remember are:

```text
Configuration  → /etc/httpd/
Document Root  → /var/www/html/
Logs            → /var/log/httpd/
```

---

# 13. Document Root

The **DocumentRoot** is the directory from where Apache serves web content.

The default document root is normally:

```text
/var/www/html/
```

For example, create a web page:

```bash
vim /var/www/html/index.html
```

Put some simple HTML inside:

```html
<html>
<head>
    <title>My Web Server</title>
</head>
<body>
    <h1>Hello from Linux Web Server</h1>
</body>
</html>
```

Save the file.

Now Apache can serve this page.

---

# 14. Access the Web Server Using IP Address

First find the server IP address:

```bash
ip addr
```

or:

```bash
ip a
```

Suppose the server IP is:

```text
192.168.1.100
```

From another machine, open a browser and enter:

```text
http://192.168.1.100
```

The browser sends an HTTP request to:

```text
192.168.1.100:80
```

Apache receives the request and serves:

```text
/var/www/html/index.html
```

So the complete flow is:

```text
Browser
   |
   | http://192.168.1.100
   |
   v
Server IP
   |
   | Port 80
   v
Firewall
   |
   v
httpd service
   |
   v
DocumentRoot
/var/www/html/
   |
   v
index.html
```

---

# 15. Testing from the Server Itself

We can also test the web server locally using `curl`.

Run:

```bash
curl http://localhost
```

or:

```bash
curl http://127.0.0.1
```

If Apache is working correctly, we should receive the HTML content.

We can also test using the server's IP:

```bash
curl http://192.168.1.100
```

Replace the IP with the actual server IP.

---

# 16. Changing the HTTP Port

By default Apache uses:

```text
80
```

The port configuration is controlled through the Apache configuration.

In:

```text
/etc/httpd/conf/httpd.conf
```

we can find the `Listen` directive.

For example:

```text
Listen 80
```

If we change it to:

```text
Listen 8080
```

Apache will listen on port 8080 instead.

After changing the configuration, restart Apache:

```bash
systemctl restart httpd
```

Then allow the new port through the firewall:

```bash
firewall-cmd --permanent --add-port=8080/tcp
```

Reload:

```bash
firewall-cmd --reload
```

Now access the web server using:

```text
http://192.168.1.100:8080
```

---

# 17. Changing the Document Root

Suppose we want to use:

```text
/website
```

instead of:

```text
/var/www/html
```

First create the directory:

```bash
mkdir /website
```

Create a web page:

```bash
vim /website/index.html
```

Then configure Apache's `DocumentRoot` to point to that directory.

For example:

```text
DocumentRoot "/website"
```

The exact configuration may also require appropriate `<Directory>` permissions for the new location.

After changing the configuration:

```bash
systemctl restart httpd
```

Then test:

```bash
curl http://localhost
```

---

# 18. Check Apache Configuration Before Restart

Before restarting Apache after making configuration changes, it is a good practice to test the configuration.

Use:

```bash
apachectl configtest
```

If everything is correct, you should get:

```text
Syntax OK
```

Then restart:

```bash
systemctl restart httpd
```

This is safer than directly restarting after every configuration change.

---
