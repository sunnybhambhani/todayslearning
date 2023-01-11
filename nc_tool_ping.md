## ping ##

"ping" is a computer networking utility which is used to test whether a particular host is reachable across an IP network.

It works by sending ICMP (Internet Control Message Protocol) echo request packets to the target host and listening for ICMP echo response packets back.

- To check if a website is up, you can use the command "ping DOMAIN_NAME"
```
$ ping google.com
PING google.com(bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e)) 56 data bytes
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=1 ttl=118 time=231 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=2 ttl=118 time=161 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=3 ttl=118 time=10.1 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=5 ttl=118 time=116 ms
^C
--- google.com ping statistics ---
5 packets transmitted, 4 received, 20% packet loss, time 4006ms
rtt min/avg/max/mdev = 10.082/129.453/231.225/80.252 ms
```

- To check if a specific IP address is reachable, you can use the command "ping IP_ADDRESSS"
```
ping 2404:6800:4009:821::200e
PING 2404:6800:4009:821::200e(2404:6800:4009:821::200e) 56 data bytes
64 bytes from 2404:6800:4009:821::200e: icmp_seq=1 ttl=59 time=8.53 ms
64 bytes from 2404:6800:4009:821::200e: icmp_seq=2 ttl=59 time=331 ms
64 bytes from 2404:6800:4009:821::200e: icmp_seq=3 ttl=59 time=150 ms
64 bytes from 2404:6800:4009:821::200e: icmp_seq=4 ttl=59 time=397 ms
64 bytes from 2404:6800:4009:821::200e: icmp_seq=5 ttl=59 time=396 ms
64 bytes from 2404:6800:4009:821::200e: icmp_seq=6 ttl=59 time=105 ms
^C
--- 2404:6800:4009:821::200e ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5006ms
rtt min/avg/max/mdev = 8.533/231.285/396.783/150.780 ms

```


Note: 
- Many networks and firewalls may block ICMP echo requests, which means that "ping" may not work as expected.
- When you run a ping command, you will see the following statistics:
  - Packets: Transmitted 5, Received 4, Lost 20% (20% loss)
  - Round trip times: min/avg/max/etc
- It works with both IPv4 and IPv6.
