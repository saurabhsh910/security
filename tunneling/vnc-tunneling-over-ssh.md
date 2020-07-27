# VNC tunneling over SSH

Basically, tunneling is a process which allows data sharing or communication between two different networks privately. Tunneling is normally performed through encapsulating the private network data and protocol information inside the public network broadcast units so that the private network protocol information visible to the public network as data. 

**Let’s Begin!!**

**Requirement:**

Server machine\(Ubuntu\):  Two network interface with activated SSH service

Local machine \(Ubuntu\): activated VNC service

Remote machine\(window\):  with install tight VNC viewer

In the following image, we are trying to explain the VNC tunneling process where a remote PC of IP 192.168.1.225 is trying to connect to 10.0.0.20 which is on INTRANET of another network. To establish a connection with the **local machine**, remote PC will create VNC tunnel which will connect with the **local** system via **SSH server machine**.

![](https://i2.wp.com/2.bp.blogspot.com/-fR03o3cn8fY/WdHrOM5SreI/AAAAAAAARxI/jYm1is-18ZoJoCD8Ii0UqauNxlxtuLR9QCLcBGAs/s1600/0.png?w=687&ssl=1)

Given the image below is describing the network configuration for **server machine \(SSH\)** where it is showing two IP 192.168.1.226 and another 10.0.0.10 as explain above.

![](https://i1.wp.com/3.bp.blogspot.com/-guC0T5hSL1k/WdHcqhpUfeI/AAAAAAAARwc/9W0Xsx8ClGsIBHw7MOpJqqP60b2g2nUYACLcBGAs/s1600/1.png?w=687&ssl=1)

Another image given below is describing network configuration for a **local machine** which is showing IP 10.0.0.20

![](https://i1.wp.com/4.bp.blogspot.com/-bH6308A8F28/WdHcrTb3GdI/AAAAAAAARwg/IwC6NxyrVAsylS0TsLW2q8WiaY3lGvAZgCLcBGAs/s1600/2.png?w=687&ssl=1)

Checking activated VNC service using the following command: netstat -tlp

| 1 |  netstat -tlp |
| :--- | :--- |


Hence from the given image, you can see the highlighted text is showing 5900 is enabled in the local machine.

![](https://i1.wp.com/4.bp.blogspot.com/-jL4Quil0KoI/WdHcrXOtLnI/AAAAAAAARwk/VwS4q293eH4wV0ud_LfPF9wZDXecs6ywgCLcBGAs/s1600/3.png?w=687&ssl=1)

Open the terminal and type using the following command to connecting to VNC machine \(IP: 10.0.0.20\) through server machine \(IP: 10.0.0.10\).vncviewer 10.0.0.20

| 1 | vncviewer 10.0.0.20 |
| :--- | :--- |


![](https://i0.wp.com/1.bp.blogspot.com/-7CotOjfmHAA/WdHcrTjCZ0I/AAAAAAAARwo/c8ZQr9U1T0YKM1_GuFyAxDaWSLL4taGXgCLcBGAs/s1600/4.png?w=687&ssl=1)

**Great!!** Local machine successfully connected

![](https://i0.wp.com/1.bp.blogspot.com/-ID-nKw0CT1o/WdHcrlYLsoI/AAAAAAAARws/hum6YTvnxy8pcgxIeFjvziJVFDUv_tQvwCLcBGAs/s1600/5.png?w=687&ssl=1)

Similarly Using tight vnc viewer remote machine \(192.168.1.225\) now trying to connect local machine \(IP: 10.0.0.10\) as shown in the given image

![](https://i2.wp.com/2.bp.blogspot.com/-f7zW4XIpnu4/WdHcr8YwI1I/AAAAAAAARww/-mYxItjloXcGFv0fGqU0psdQFT6UcFCmgCLcBGAs/s1600/6.png?w=687&ssl=1)

Since they belong to the different network, therefore, he receives network error.

![](https://i2.wp.com/4.bp.blogspot.com/-qetQYWxqgRI/WdHcr-5eBRI/AAAAAAAARw0/xoDFRy8H9yc0_RLrwewk0PISpMClyjnBQCLcBGAs/s1600/7.png?w=687&ssl=1)

Follow given below step to connect remote machine to the local machine via ssh server.

* Open TightVNC connection and enter the local machine IP: **0.0.20** with port **5900.**
* **Enable** SSH tunneling
* Now enter ssh server IP: **168.1.226** with port **22** and ssh server username: **ubuntu.**

![](https://i1.wp.com/1.bp.blogspot.com/-fxNW_-jyasI/WdHcsEq-GII/AAAAAAAARw4/hMrb7IarzAgUOy3hBkYp0jETW8wGw4bVgCLcBGAs/s1600/8.png?w=687&ssl=1)

**Congrats!!!** The remote machine had successfully connected with the local machine through VNC.

\*\*\*\*

