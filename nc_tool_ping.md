## ping ##

"ping" is a computer networking utility which is used to test whether a particular host is reachable across an IP network.

It basiclaly works by sending ICMP (Internet Control Message Protocol) echo request packets to the target host and listens for ICMP echo response packets back.

This is one of the most widely used utilities for troubleshooting network problems.

- To check if a website is up, you can use the command "ping DOMAIN_NAME".
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

- To check if a specific IP address is reachable, you can use the command "ping IP_ADDRESSS".
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

- To specify specific number of echo request use `-c` flag.
```
$ ping -c 4 google.com
PING google.com(bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e)) 56 data bytes
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=1 ttl=118 time=13.0 ms
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=2 ttl=118 time=13.6 ms
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=3 ttl=118 time=146 ms
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=4 ttl=118 time=63.0 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 13.038/58.942/146.136/54.264 ms
```

- To specifiy interval between 2 echo requests, use `-i` flag. In below example it will wait for 20 seconds.
```
$ ping -c 4 -i 20 google.com
PING google.com(bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e)) 56 data bytes
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=1 ttl=118 time=10.9 ms
64 bytes from bom07s35-in-x0e.1e100.net (2404:6800:4009:82c::200e): icmp_seq=2 ttl=118 time=17.6 ms
```

- To specify size of packet to be sent, use `-s` flag.
```
$ ping -s 30 -c 3 google.com
PING google.com(bom12s20-in-x0e.1e100.net (2404:6800:4009:830::200e)) 30 data bytes
38 bytes from bom12s20-in-x0e.1e100.net (2404:6800:4009:830::200e): icmp_seq=1 ttl=118 time=9.99 ms
38 bytes from bom12s20-in-x0e.1e100.net (2404:6800:4009:830::200e): icmp_seq=2 ttl=118 time=130 ms
38 bytes from bom12s20-in-x0e.1e100.net (2404:6800:4009:830::200e): icmp_seq=3 ttl=118 time=151 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 9.991/96.763/150.712/61.960 ms
```

- To get just the summarized result, use `-q` flag.
```
$ ping -q -c 3 google.com
PING google.com(bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e)) 56 data bytes

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 7.333/119.203/239.898/95.149 ms
```

- To stop the operation after specific interval(as in time), use `-w` flag, in below example ping will automatically stop after 5 seconds.
```
$ ping -w 5 google.com
PING google.com(bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e)) 56 data bytes
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=1 ttl=118 time=116 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=2 ttl=118 time=38.7 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=3 ttl=118 time=10.3 ms
64 bytes from bom12s12-in-x0e.1e100.net (2404:6800:4009:822::200e): icmp_seq=5 ttl=118 time=6.92 ms

--- google.com ping statistics ---
5 packets transmitted, 4 received, 20% packet loss, time 4024ms
rtt min/avg/max/mdev = 6.921/43.113/116.488/44.127 ms
```

- To ping IPv4 address use `-4` flag.
```
$ ping -c 5  google.com -4
PING  (142.250.192.14) 56(84) bytes of data.
64 bytes from bom12s14-in-f14.1e100.net (142.250.192.14): icmp_seq=1 ttl=117 time=10.1 ms
64 bytes from bom12s14-in-f14.1e100.net (142.250.192.14): icmp_seq=2 ttl=117 time=56.2 ms
64 bytes from bom12s14-in-f14.1e100.net (142.250.192.14): icmp_seq=3 ttl=117 time=282 ms
64 bytes from bom12s14-in-f14.1e100.net (142.250.192.14): icmp_seq=4 ttl=117 time=203 ms
64 bytes from bom12s14-in-f14.1e100.net (142.250.192.14): icmp_seq=5 ttl=117 time=22.5 ms

---  ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 10.050/114.709/282.204/108.319 ms
```

- To ping IPv6 address use `-6` flag.
```
$ ping -c 5  google.com -6
PING google.com(bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e)) 56 data bytes
64 bytes from bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e): icmp_seq=1 ttl=118 time=9.45 ms
64 bytes from bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e): icmp_seq=2 ttl=118 time=10.3 ms
64 bytes from bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e): icmp_seq=3 ttl=118 time=47.8 ms
64 bytes from bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e): icmp_seq=4 ttl=118 time=69.7 ms
64 bytes from bom12s18-in-x0e.1e100.net (2404:6800:4009:82b::200e): icmp_seq=5 ttl=118 time=91.5 ms

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 9.450/45.743/91.463/32.387 ms
```

Note: 
- Many networks and firewalls may block ICMP echo requests, which means that "ping" may not work as expected.
- When you run a ping command, you will see the following statistics:
  - Packets: Transmitted 5, Received 4, Lost 20% (20% loss)
  - Round trip times: min/avg/max/etc
- It works with both IPv4 and IPv6.
