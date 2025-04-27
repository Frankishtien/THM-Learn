# Active Reconnaissance


## will learn ...
![Critical](https://img.shields.io/badge/tool-ping-red)
![Critical](https://img.shields.io/badge/tool-traceroute-red)
![Critical](https://img.shields.io/badge/tool-telnet-red)
![Critical](https://img.shields.io/badge/tool-netcat-red)
- Web-browser

---




##  ``Web Browser``

> while browsing a web page, you can press **``Ctrl+Shift+I``** on a PC or **``Option + Command + I (⌥ + ⌘ + I)``** on a Mac to open the Developer Tools on Firefox.


##  ``ping``

> If you prefer a pickier definition, the ping is a command that sends an ICMP Echo packet to a remote system. If the remote system is online, and the ping packet was correctly routed and not blocked by any firewall, the remote system should send back an ICMP Echo Reply. Similarly, the ping reply should reach the first system if appropriately routed and not blocked by any firewall.

`` for example ``

```
user@AttackBox$ ping -c 5 10.10.157.161
PING 10.10.157.161 (10.10.157.161) 56(84) bytes of data.
64 bytes from 10.10.157.161: icmp_seq=1 ttl=64 time=0.636 ms
64 bytes from 10.10.157.161: icmp_seq=2 ttl=64 time=0.483 ms
64 bytes from 10.10.157.161: icmp_seq=3 ttl=64 time=0.396 ms
64 bytes from 10.10.157.161: icmp_seq=4 ttl=64 time=0.416 ms
64 bytes from 10.10.157.161: icmp_seq=5 ttl=64 time=0.445 ms

--- 10.10.157.161 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4097ms
rtt min/avg/max/mdev = 0.396/0.475/0.636/0.086 ms
```


---
## ``traceroute``

> As the name suggests, the traceroute command traces the route taken by the packets from your system to another host. The purpose of a traceroute is to find the IP addresses of the routers or hops that a packet traverses as it goes from your system to a target host. This command also reveals the number of routers between the two systems. It is helpful as it indicates the number of hops (routers) between your system and the target host. However, note that the route taken by the packets might change as many routers use dynamic routing protocols that adapt to network changes.

> On Linux and macOS, the command to use is **``traceroute 10.10.157.161``**, and on MS Windows, it is **``tracert 10.10.157.161``**. traceroute tries to discover the routers across the path from your system to the target system.


`` for example ``

```
traceroute 10.10.157.161
traceroute to 10.10.157.161 (10.10.157.161), 30 hops max, 60 byte packets
 1  10.8.0.1 (10.8.0.1)  92.964 ms  93.667 ms  93.734 ms
 2  10.10.157.161 (10.10.157.161)  94.504 ms  95.115 ms  95.582 ms

```

---

## ``TELNET ``

> The TELNET (Teletype Network) protocol was developed in 1969 to communicate with a remote system via a command-line interface (CLI). Hence, the command telnet uses the TELNET protocol for remote administration. The default port used by telnet is 23. From a security perspective, telnet sends all the data, including usernames and passwords, in cleartext. Sending in cleartext makes it easy for anyone, who has access to the communication channel, to steal the login credentials. The secure alternative is SSH (Secure SHell) protocol.


``for exmple``

```
pentester@TryHackMe$ telnet 10.10.157.161 80
Trying 10.10.157.161...
Connected to 10.10.157.161.
Escape character is '^]'.
GET / HTTP/1.1
host: telnet

HTTP/1.1 200 OK
Server: nginx/1.6.2
Date: Tue, 17 Aug 2021 11:13:25 GMT
Content-Type: text/html
Content-Length: 867
Last-Modified: Tue, 17 Aug 2021 11:12:16 GMT
Connection: keep-alive
ETag: "611b9990-363"
Accept-Ranges: bytes

```

-------

## ``netcat``

> Netcat or simply nc has different applications that can be of great value to a pentester. Netcat supports both TCP and UDP protocols. It can function as a client that connects to a listening port; alternatively, it can act as a server that listens on a port of your choice. Hence, it is a convenient tool that you can use as a simple client or server over TCP or UDP.


``for example ``

```
pentester@TryHackMe$ nc 10.10.88.124 80
GET / HTTP/1.1
host: netcat

HTTP/1.1 200 OK
Server: nginx/1.6.2
Date: Tue, 17 Aug 2021 11:39:49 GMT
Content-Type: text/html
Content-Length: 867
Last-Modified: Tue, 17 Aug 2021 11:12:16 GMT
Connection: keep-alive
ETag: "611b9990-363"
Accept-Ranges: bytes
```


option	  |      meaning
----------|--------------------------------------------------------
-l	      |     Listen mode
-p	      |    Specify the Port number
-n	      |      Numeric only; no resolution of hostnames via DNS
-v	      |      Verbose output (optional, yet useful to discover any bugs)
-vv	      |     Very Verbose (optional)
-k	      |    Keep listening after client disconnects



--------------------
 ## ``comparasion ``


Command	         |      Example
-----------------|--------------------------------------------
ping	           |     ping -c 10 10.10.88.124 on Linux or macOS
ping	           |       ping -n 10 10.10.88.124 on MS Windows
traceroute       |       	traceroute 10.10.88.124 on Linux or macOS
tracert	         |       tracert 10.10.88.124 on MS Windows
telnet	         |         telnet 10.10.88.124 PORT_NUMBER
netcat as client |           nc 10.10.88.124 PORT_NUMBER
netcat as server |       nc -lvnp PORT_NUMBER





