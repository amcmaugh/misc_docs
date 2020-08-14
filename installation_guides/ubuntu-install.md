# Ubuntu Server 20.04 Installation Steps

## Create a bootable Ubunutu USB drive

Create a bootable USB drive by following the intructions for [Linux][linux-usb-stick], [Windows][windows-usb-stick] or [Apple macOS][apple-usb-stick].

## Install Ubuntu 20.03 Server Edition

1. Choose language and press **Enter**.

1. Select **Keyboard** layout and press **Enter**.

1. If connected to a network you will recieve an IP address from your DHCP server. Select **Done** to continue.

1. If you need a proxy server to connect to the internet, enter its details here. Otherwise, leave it empty and select **Done**.

1. The installer will automatically select an Ubunutu archive mirror. Select **Done** to continue.

1. For storage, ensure to select the 'Use an entire disk' and 'Set up this disk as an LVM group' options. Sekect **Done** to continue.

1. Partition sizes can be customized here, slect **Done** to continue.

1. Choose **Continue** to partition the drive.

1. Fill in the profile section with the following format:

    * Name:  IOT-WORX Gateway 
    * Server's name: gateway-gg-{CustomerRef}-{#} e.g., gateway-gg-aibr-001
    * Username: iotworx
    * Password: ****

1. Check the 'Install OpenSSH server' option and select **Done**.

1. Check the 'aws-cli' option and select **Done**.

1. Once the installation process completes press **Enter** to reboot the system. Remove the installation USB drive at this stage.


## Update and Upgrade the system

Log into the server with a user with root privileges, such as the account created above. Run the following commands to update the system.

```bash
$ sudo apt update
```

To see a list of upgradable packages
```bash
$ sudo apt list --upgradable
```

To run the upgrade
```bash
$ sudo apt upgrade
```

To clean up after an upgrade
```bash
$ sudo apt autoremove
$ sudo apt clean
```


## Secure SSH server

In order to secure the SSH server we need to change the port from the default **22** to a random port, higher than **1024**, and diallow remote access to the root account.

Open the the SSH configuration file, with `nano` or alternative text editor, and make the following changes.
```bash
sudo nano /etc/ssh/sshd_config
```
Find the commented line **#Port 22** and change this to a different port number e.g., 

```bash
Port 2288
```

Find the commented line **#PermitRootLogin prohibit-password**  and change this to no e.g.,

```bash
PermitRootLogin no
```

n.b. In `nano` press ``Ctrl`` + `O` to save your changes followed by ``Ctrl`` + ``X`` to exit.


## Configure Ubuntu Firewall UFW

Add a new rule to the Ubuntu firewall to allow traffic via the custom SSH port we defined above.

```bash
$ sudo ufw allow 2288/tcp
```

Enable the firewall rules.

```bash
$ sudo ufw enable
```

Verify the rule has been applied.
```
$ sudo ufw status
```

To list all firwall rules use the following command.

```bash
$ sudo ufw status verbose
```


## Remove Snapd

Snapd is a package manager that won't be needed. Run the following command to disable and remove it.

```bash
$ sudo apt autoremove --purge snapd
```


## Reboot System

Reboot the system.
```bash
$ sudo reboot 
```


[linux-usb-stick]: https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview
[windows-usb-stick]: https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview
[apple-usb-stick]: https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview