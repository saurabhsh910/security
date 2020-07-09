# General tips and tricks

## Disposable email <a id="disposable-email"></a>

If you are signing up for a lot of accounts you can use a disposable email. You just enter the email account you want for that second, and then you can view it. But remember, so can everyone else.  
[https://www.mailinator.com](https://www.mailinator.com/)

## Base64 encode/decode <a id="base64-encodedecode"></a>

```text
import base64

encoded = base64.b64encode("String to encode")
print encoded

decoded = base64.b64decode("aGVqc2Fu")
print decoded
```

## Default passwords <a id="default-passwords"></a>

[http://www.defaultpassword.com/](http://www.defaultpassword.com/)

## Getting GUI on machine that does not have RDP or VNC <a id="getting-gui-on-machine-that-does-not-have-rdp-or-vnc"></a>

You can forward X over SSH.  
[http://www.vanemery.com/Linux/XoverSSH/X-over-SSH2.html](http://www.vanemery.com/Linux/XoverSSH/X-over-SSH2.html)



## Safe way to SSH in AWS

### Never copy your ssh keys to a remote host, even for testing

Bad habits should be shot down quick, as Rjayroach says.

The better ways:

### Using ssh -A

Just to elaborate on the two answers before mine for those who are not so conversant with ssh.

You first add your pem file to ssh agent on your local machine

```text
ssh-add ~/.aws/pems/your-ec2-pem-file.pem
```

ssh agent is now keeping your key in memory. Then you can now ssh into the public ec2 without using the `-i` flag and path to pem file, but instead use the `-A` flag to forward this key to the remote host \(so that you can use it again\):

```text
ssh -A ec2-user@18.129.61.41
```

Once connected to the remote host, you can ssh to the other machine. The key has been forwarded to the remote host by ssh-agent \(no need to copy your pem file to the remote host!\):

```text
[ec2-user@ip-18-129-61-41 ~]$ ssh ec2-user@10-0-2-165     # connect to your private subnet instance  
[ec2-user@ip-10-0-2-165 ~]$                               # voila!  
```

This, as mentioned is much more secure and a better way to access the private instance. However, its not completely secure. It still has some issues. See this [article](https://heipei.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful) and the subsequent [discussion](https://news.ycombinator.com/item?id=9425805) on hackernews.

A better tool to use in this situation is `ProxyCommand`.

### Using ProxyCommand instead of ssh -A

The basics of this is to use the public subnet ec2 as a jump host to the target host \(the ec2 in the private subnet\).

First you add the two remote hosts to ssh config file: `~/.ssh/config` on your local machine. You may need to create this file if it does not exist: `touch ~/.ssh/config`.  
Open the file in an editor:

```text
Host webserver01
    HostName 18.129.61.41
    IdentityFile ~/.aws/pems/your-ec2-pem-file.pem
    User ec2-user
Host dbserver01
    HostName 10.0.2.165
    IdentityFile ~/.aws/pems/your-ec2-pem-file.pem
    Port 22
    User ec2-user
    ProxyCommand ssh -q -W %h:%p webserver01
```

Where the command `ProxyCommand ssh -q -W %h:%p webserver01` means run ssh in quiet mode \(using `-q`\) and in stdio forwarding \(using `-W`\) mode and redirect the connection through an intermediate host `webserver01`.

Then to connect to the jump host \(our `webserver01`\), you can just:

```text
ssh webserver01
```

Or to connect to the `dbserver01`

```text
ssh dbserver01
```

That's it!

You can add the `-v` flag to ssh for verbose logging if you encounter any issues \(`ssh -v dbserver01`\)

{% embed url="https://aws.amazon.com/blogs/security/securely-connect-to-linux-instances-running-in-a-private-amazon-vpc/" %}



