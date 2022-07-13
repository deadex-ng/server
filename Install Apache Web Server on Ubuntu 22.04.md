# Install Apache Web Server on Ubuntu 22.04

## Introduction

The Apache HTTP server is the most popular web server in the world. It has powerful features including loadable dynamic modules, auto indexing and load balancing. It's actively maintained by the Apache Software Foundation. 

In this guide, we'll explain how to install Apache web server on Ubuntu 22.04 server. 

## Before You Begin

Before you begin, review the following:

1. Ensure that you have a non-root user with `sudo` privileges on your Ubuntu 22.04 server. 
2. Enable a firewall to block non-essential ports.


## Installing Apache

Apache is available in the official repositories of Ubuntu. Therefore, we install it using conventional package management tools.

First, update the system repositories to reflect the latest changes:

```bash
sudo apt update
```

Then, install the `Apache` package with:

```bash
sudo apt install apache2
```

## Verifying the Installation

Verify that Apache was successfully installed by running the command `apache --version`. This should display the version number of the installation of Apache. For reference, this guide is using `Apache` version `2.4.52`.

## Configuring the Firewall

We need to edit the firewall settings to allow outside access to Apache default web ports. 

Upon installation, Apache registers itself with UFW to provide a few application profiles that can be used to enable or disable access to Apache through a firewall.

List the `ufw` application profiles using:

```bash
sudo ufw app list
```

The command returns a list of application profiles:

```bash
Available applications:
  Apache
  Apache Full
  Apache Secure
  CUPS
```

There are three profiles for Apache:

- `Apache`: This profile opens only port `80` (normal, unencrypted web traffic).
- `Apache Full`: This profile opens both port `80` (normal, unencrypted web traffic) and port `443` (TLS/SSL encrypted traffic). 
- `Apache Secure`: This profile opens only port `443` (TLS/SSL encrypted traffic).

It is recommended that we enable the most restrictive profile that will allow the traffic we have configured. We have not configured SSL for our server yet. Therefore, we will only need to allow traffic on port `80`:

```bash
sudo ufw allow 'Apache'
```

 Verify the change by checking:

```bash
sudo ufw status
```

The output will display a list of allowed HTTP traffic:

```bash
Status: active

To                         Action      From
--                         ------      ----
Apache                     ALLOW       Anywhere                  
Apache (v6)                ALLOW       Anywhere (v6)             
```

## Checking the Web Server

Once Apache has been installed, Ubuntu starts Apache. The web server will already be up and running. 

We can check the `systemd` init system to make sure the server is running:

```bash
sudo systemctl status apache2
```

The output will display the status of the web server:

```bash
 apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor pres>
     Active: active (running) since Sun 2022-07-10 15:13:31 CAT; 7min ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 4647 (apache2)
      Tasks: 55 (limit: 2251)
     Memory: 5.1M
        CPU: 346ms
     CGroup: /system.slice/apache2.service
             ├─4647 /usr/sbin/apache2 -k start
             ├─4649 /usr/sbin/apache2 -k start
             └─4650 /usr/sbin/apache2 -k start
```

The output confirms that the service is running successfully. However, the best way to test this is to request a page from the web server.

We can request a page from the web server by browsing to `http://localhost` or `http://ip_address_of_the_machine`. This will display a default landing web page for Apache.

## Managing the Apache Process

Now that the web server is up and running. We can go over some basic management commands using `systemctl`.

To stop the web server:

```bash
sudo systemctl stop apache2
```

To start the web server when it is stopped:

```bash
sudo systemctl start apache2
```

To stop then start the web server again:

```bash
sudo systemctl restart apache2
```

In the case that you have made configuration changes, Apache can reload without dropping connections:

```bash
sudo systemctl reload apache2
```

By default Apache is configured to start automatically when the server boots. If this is not what you want, you can disable this:

```bash
sudo systemctl disable apache2
```

To re-enable the service to start at boot:

```bash
sudo systemctl enable apache2
```

## Important Apache Files and Directories

Let us now familiarize ourselves with a few important Apache files and directories. 

#### Content

- `/var/www/html`: This directory contains the actual web content that is served to users. The default Apache landing page is served from this directory. This can be changed by editing the Apache configuration files. 

#### Server Configuration

- `/etc/apache2`: All the Apache configuration files reside in this directory.
- `/etc/apache2/apache2.conf`: The main Apache configuration file. Modifications to this file apply to the Apache global configuration. It is also responsible for loading other configuration files.
- `/etc/apache2/ports.conf`: This configuration file specifies the ports that Apache will listen on. By default, Apache listens on port `80` and additionally listens on port `443` if SSL is enabled.
- `/etc/apache2/sites-available`: This directory stores site-specific configuration files for each virtual host. These are sites which are available but not necessarily enabled. Apache does not use the configuration files in this directory unless they are linked to the `sites-enabled` directory.
- `/etc/apache2/sites-enabled`: This stores sites that are enabled. These are created by linking the configuration files found in `sites-available` directory. Apache reads the configuration files and links when it starts or reloads to compile a configuration. 
- `/etc/apache2/conf-available`: This directory contains specialized and local configuration files, and links to configuration files set up by other applications.
- `/etc/apache2/conf-enabled`: This holds symlinks to files in the `/etc/apache2/conf-available` directory. Once a configuration file is symlinked, it is enabled the next time Apache restarts. 
- `/etc/apache2/mods-available`: The directory that has the configuration files to load modules and configure them.
- `/etc/apache2/mods-enabled`: This holds the symlinks to files in the `/etc/apache2/mods-available` directory.

#### Server Logs

- `/var/log/apache2/access.log`: Every request to the web server is recorded in this file. 


- `/var/log/apache2/error.log`: All error logs are stored in this file. The `LogDirective` in Apache configuration specifies how much detail the error logs will contain. 

## Conclusion

In this guide, we looked at installing Apache on Ubuntu 22.04. We also looked at configuring the firewall to allow Apache traffic on port `80` and some important Apache files and directories. 