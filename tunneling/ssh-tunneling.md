# SSH tunneling

 **SSH Tunnel:**  Tunneling is the concept to encapsulate the network protocol to another protocol here we put into SSH, so all network communication is encrypted. Because tunneling involves repackaging the traffic data into a different form, perhaps with encryption as standard, a third use is to hide the nature of the traffic that is run through the tunnels.



#### **Types of SSH Tunneling:**     

1. Dynamic SSH tunneling
2. Local SSH tunneling
3. Remote SSH tunneling

{% hint style="info" %}
> Set one of the ‘Local’ or ‘Remote’ radio buttons, depending on whether you want to forward a local port to a remote destination \(‘Local’\) or forward a remote port to a local destination \(‘Remote’\). Alternatively, select ‘Dynamic’ if you want PuTTY to provide a local SOCKS 4/4A/5 proxy on a local port \(note that this proxy only supports TCP connections; the SSH protocol does not support forwarding UDP\).

* Local -- Forward local port to remote host.
* Remote -- Forward remote port to local host.
* Dynamic -- Act as a [SOCKS proxy](https://en.wikipedia.org/wiki/SOCKS). This requires special support from the software that connects to it, however the destination address is obtained dynamically at runtime rather than being fixed in advance.

The configuration of the resources you are accessing will determine which of the three options you need to use. This isn't a case of one option being "better" than the others.

Use **local** if you have a service running on a machine that can be reached from the remote machine, and you want to access it directly from the local machine. After setting up the tunneling you will be able to access the service using your local host IP \(127.0.0.1\)

Use **remote** if you have a service that can be reached from the local machine, and you need to make it available to the remote machine. It opens the listening socket on the machine you have used SSH to log into.

**Dynamic** is like local, but on the client side it behaves like a SOCKS proxy. Use it if you need to connect with a software that expects SOCKS forwarding.
{% endhint %}

#### \*\*\*\*

**Objective:**  To establish an SSH connection between remote PC and the local system of the different network.

Here I have set my own lab which consists of three systems in the following network:

**SSH server** \(two Ethernet interface\) 

IP 192.168.1.104 connected with the remote system

IP 192.168.10.1 connected to local network system 192.168.10.2

**SSH client** \(local network\) holds IP 192.168.10.2

**Remote system** \(outside the network\)

In the following image, we are trying to explain the SSH tunneling process where a remote PC is trying to connect to 192.168.10.2 which is on INTRANET of another network. To establish a connection with **an SSH client \(raj\)**, remote PC will create an SSH tunnel which will connect with the local system via **SSH server \(Ignite\)**.

**NOTE:** **Service SSH must be activated**

![](https://i0.wp.com/3.bp.blogspot.com/-g-ZNXGv1BaA/Wqt0_PlQEVI/AAAAAAAAVTA/ahXwB4NOpPMYx5hr4grrwVdvG9TuTJIWgCLcBGAs/s1600/0.jpg?w=687&ssl=1)

## Dynamic Port forwarding

#### **Step for Dynamic SSH tunneling**

* Choose option **SSH &gt;Tunnel** given in the left column of the category.
* Give new port forwarded as **7000** and connection type as **dynamic** and click on ADD at last.

![](https://i0.wp.com/3.bp.blogspot.com/-nwS-sQY8fzM/Wqtln3sFnzI/AAAAAAAAVSo/CXEaAdDHH7EL-MMP3gfoJGG6sfAwlpDLwCEwYBhgL/s1600/5.png?w=687&ssl=1)

Now connect to SSH server 192.168.1.104 via port 22 and then click on **open** when all things get set.

![](https://i1.wp.com/3.bp.blogspot.com/-ejFGREsjV94/Wqtlnxo1xQI/AAAAAAAAVSw/hu96xKmF0nUanZs3qaMTWaVgTHcaIDTZQCEwYBhgL/s1600/6.png?w=687&ssl=1)

First, it will connect to the SSH server as you can see we are connected with SSH server \(Ignite\).

![](https://i1.wp.com/1.bp.blogspot.com/-S26Y2Y_sqks/WqtloQ6SPaI/AAAAAAAAVSw/XI51HhgYVm0TxgjMiWjkfa3QokECU8T3ACEwYBhgL/s1600/7.png?w=687&ssl=1)

Now login into putty again and give IP of client system as Host Name **192.168.10.2** and Port **22** for SSH then click on **open**.

![](https://i0.wp.com/3.bp.blogspot.com/-gtb0mqvYy_U/Wqtloqg_2bI/AAAAAAAAVSw/kTTi3IOQXloLLXV_t7lzJFyvmgMcJdPNQCEwYBhgL/s1600/8.png?w=687&ssl=1)

The open previous running window of putty choose **Proxy** option from the category and follow given below step:

* Select proxy type as **SOCKS 5**
* Give proxy hostname as 127.0.0.1 and port 7000
* Click on open to establish a connection.

![](https://i2.wp.com/3.bp.blogspot.com/-xSmPnIxevrI/Wqtlo-CksLI/AAAAAAAAVSo/M63622v1yXkBeZJ25px7MddBiTq7jUc4gCEwYBhgL/s1600/9.png?w=687&ssl=1)

**Awesome!!** We have successfully access SSH client \(raj\) via port 7000



|  ssh -D 7000 ignite@192.168.1.104 |
| :--- |


Enter the user’s password for login and get access to the **SSH server** as shown below.

![](https://i0.wp.com/3.bp.blogspot.com/-kOWmMSGfKvM/Wqtle0btUtI/AAAAAAAAVS0/56p-Tk_JHtEpV20JCueK9wVIz-_prblGQCEwYBhgL/s1600/11.png?w=687&ssl=1)

Next, we need to set a network proxy for enabling socksv5 and for that follow below steps.

* In your web browser “Firefox” go to option for general setting tab and open **Network Proxy**.
* Choose **No Proxy**
* Enable **socksv5**

Add **localhost, 127.0.0.1** as Manual proxy

![](https://i2.wp.com/1.bp.blogspot.com/-ZyKsa3XA5d0/WqtlfIil67I/AAAAAAAAVSs/-7KrjJgmXiUTFaqDJ7Tji79bncazK9KxwCEwYBhgL/s1600/12.png?w=687&ssl=1)

So from given below image, you can perceive that now we able to connect with the client: 192.168.10.2 via port 80.

![](https://i1.wp.com/1.bp.blogspot.com/-ZE_iwjPDQ8g/WqtlfCthDYI/AAAAAAAAVSk/Im7bfKlsprQrHPgHXRqPgtChL7LxQW8PACEwYBhgL/s1600/13.png?w=687&ssl=1)

#### **Dynamic SSH Tunneling through Kali Linux on Port 22**

Now connect to client machine through given below command:ssh -D 7000 ignite@192.168.1.104

| 1 | ssh -D 7000 ignite@192.168.1.104 |
| :--- | :--- |


![](https://i1.wp.com/2.bp.blogspot.com/-rYwmJWsghBI/WqtlfZpwMUI/AAAAAAAAVSo/WCW8_QdbMYE3B4WJs0PFVXnjD34z__MnQCEwYBhgL/s1600/14.png?w=687&ssl=1)

Install tsocks through apt repository using the command:apt install tsocks

| 1 | apt install tsocks |
| :--- | :--- |


**tsocks** – Library for intercepting outgoing network connections and redirecting them through a SOCKS server. 

![](https://i2.wp.com/1.bp.blogspot.com/-ijGI1kK7tB0/Wqtlf5FOuDI/AAAAAAAAVSk/lL7GY_E_YSsmzBY8SpDeE2jUjymss7DagCEwYBhgL/s1600/15.png?w=687&ssl=1)

Open the **tsocks.conf** file for editing socks server IP and port, in our case we need to mention below two lines and then save it.

Server = 127.0.0.1

Server\_port = 7000

![](https://i1.wp.com/4.bp.blogspot.com/-uxnvyDOcRPY/WqtlgWHDEgI/AAAAAAAAVSo/4vpULtzxKZMdhGYzhISLWZSWp81viNSmACEwYBhgL/s1600/16.png?w=687&ssl=1)

Now connect to SSH client with the help tsocks using given below command.tsocks ssh raj@192.168.10.2

| 1 | tsocks ssh raj@192.168.10.2 |
| :--- | :--- |


Enter the password and enjoy the access of SSH client.

![](https://i1.wp.com/2.bp.blogspot.com/-6pTuYVoGmOQ/Wqtlg9AQS7I/AAAAAAAAVS0/BFHcZO9NLJUfVYm3lZfbDOZJA_iv4fI0gCEwYBhgL/s1600/17.png?w=687&ssl=1)

## **Local port forwarding**

**Step for SSH Local tunneling**

* Use putty to connect **SSH server** \(**192**.**168.1.104**\) via **port 22** and choose option **SSH &gt;Tunnel** given in the left column of the category.

![](https://i2.wp.com/4.bp.blogspot.com/-i8I0Ds8iCKk/WqtlhWb7KII/AAAAAAAAVSw/uXVB0BzP78kOMh_B5g2bCiEUDJtgLsjtwCEwYBhgL/s1600/18.png?w=687&ssl=1)

* Give new **port forwarded** as **7000** and connection type as **local** 
* Destination address **as 198.168.10.2:22** for establishing a connection with the specific client and click on ADD at last.
* Click on **open** when all things get set.

![](https://i2.wp.com/2.bp.blogspot.com/-uatTL4jfgGc/Wqtlhwi78CI/AAAAAAAAVSs/wi4cit9FcgIo5MPd7REJ2Ut6MDLAMM8QwCEwYBhgL/s1600/19.png?w=687&ssl=1)

First, this will establish a connection between the remote pc and SSH server.

![](https://i0.wp.com/2.bp.blogspot.com/-9TIwzLhX7nQ/WqtliXth0nI/AAAAAAAAVSk/zazt3n4TS-0Tw0j_Cd6VMbofPyKcRGpIACEwYBhgL/s1600/20.png?w=687&ssl=1)

Open a new window of putty and follow given below step:

* Give hostname as **localhost** and port **7000** and connection type **SSH**.
* Click on **open** to establish a connection.

![](https://i1.wp.com/4.bp.blogspot.com/-_sQu2NDdv5U/WqtliTMJA8I/AAAAAAAAVSw/WchGs55Q38k-6d9FHqEei8R6eNfc1VtrwCEwYBhgL/s1600/21.png?w=687&ssl=1)

**Awesome!!** We have successfully access SSH client via port 7000 

![](https://i0.wp.com/3.bp.blogspot.com/-piTTwDnkStQ/Wqtli2gI6oI/AAAAAAAAVSs/gzYAitiSaHEolhtku8UTceFPcpNOGDrGQCEwYBhgL/s1600/22.png?w=687&ssl=1)

#### **Local SSH Tunneling through Kali Linux**

Now again we switch into Kali Linux for local tunneling which is quite easy as compared to dynamic. Execute given below command for forwarding port to the local machine.ssh -L 7000:192.168.10.2:22 ignite@192.168.1.104

| 1 | ssh -L 7000:192.168.10.2:22 ignite@192.168.1.104 |
| :--- | :--- |


![](https://i0.wp.com/3.bp.blogspot.com/-R-1BryHJjJA/WqtljcVJtsI/AAAAAAAAVSk/F2oayKG4rR00swNlYsGz24gs_vIEgkDgACEwYBhgL/s1600/23.png?w=687&ssl=1)

Now open a new terminal and type below command for connecting to SSH client.ssh raj@127.0.0.1 -p 7000

| 1 | ssh raj@127.0.0.1 -p 7000 |
| :--- | :--- |


**Awesome!!** We have successfully access SSH client via port 7000 

![](https://i2.wp.com/1.bp.blogspot.com/-q1vVSVUUoxc/WqtljyETo3I/AAAAAAAAVSw/_5Z9foFjUhYynb3Lq3-nBspPWdqNOvaBgCEwYBhgL/s1600/24.png?w=687&ssl=1)

## **Remote port forwarding**

**Step for remote tunneling**

* Enter remote system IP **192**.**168.1.108**
* Mention port 22
* Go to SSH&gt;tunnel options

![](https://i2.wp.com/3.bp.blogspot.com/-M4URZaskwVs/WqtlkVnuTeI/AAAAAAAAVSs/vEJ8bD4Yvjg6ZPWXcgq7bNAXY3qxkToRACEwYBhgL/s1600/25.png?w=687&ssl=1)

* Give new **port forwarded** as **7000** and connection type as **Remote**
* Destination address **as 198.168.10.2:22**for establishing a connection with the specific client and click on ADD at last.
* Click on **open** when all things get set.

![](https://i0.wp.com/1.bp.blogspot.com/-LUQq2zs4iC8/WqtlkzWAszI/AAAAAAAAVSs/khuZzpfXpc8kLBGlEbrQGiUy6NIp88WNACEwYBhgL/s1600/26.png?w=687&ssl=1)

Now the server will get connected to Remote system as shown in below image.

![](https://i0.wp.com/1.bp.blogspot.com/-_VxUi3R8qSA/WqtllbHXbrI/AAAAAAAAVSk/x84bTafGXp8eMAwd8A0DYOaNxgaXu2cUACEwYBhgL/s1600/27.png?w=687&ssl=1)

Come back to the remote system and enter the following command to with SSH client machine.ssh raj@127.0.0.1 -p 7000

| 1 | ssh raj@127.0.0.1 -p 7000 |
| :--- | :--- |


From given below image you can observe that we had successfully connected with SSH client machine via port 7000.

![](https://i2.wp.com/1.bp.blogspot.com/-OHj-m-ojFd4/Wqtll2STY3I/AAAAAAAAVSw/Zy5OXdm4TwYGfUFo-lSSHOxSejSwC7x6gCEwYBhgL/s1600/28.png?w=687&ssl=1)

#### **Remote SSH Tunneling through Ubuntu**

If you are not willing to use putty for remote tunneling then you can execute the following commandssh -R 7000:192.168.10.2:22 root@192.168.1.108

| 1 | ssh -R 7000:192.168.10.2:22 root@192.168.1.108 |
| :--- | :--- |


Here 192.168.1.10.2 is our local client \(raj\) IP and 192.168.1.108 is our remote system IP.

![](https://i2.wp.com/2.bp.blogspot.com/-TMn0zCxxG4w/WqtlmbPsWNI/AAAAAAAAVSo/cUg4_9YSRHc59OqCs0sdVRJMv3cxdHcgwCEwYBhgL/s1600/29.png?w=687&ssl=1)

Come back to the remote system and enter the following command to with SSH client machine.ssh raj@127.0.0.1 -p 7000

| 1 | ssh raj@127.0.0.1 -p 7000 |
| :--- | :--- |


From given below image you can observe that we had successfully connected with SSH client machine via port 7000.

![](https://i0.wp.com/1.bp.blogspot.com/-jnp4XG21PEc/Wqtlnfm9yFI/AAAAAAAAVSw/NCTnhwfdZ5AjwE07Obs3P2d2km7vHYL5wCEwYBhgL/s1600/30.png?w=687&ssl=1)

