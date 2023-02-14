## traceroute ##

traceroute is a tool to trace the path of an IP packet as it traverses routers, and it is a great utility for checking the route from a source to a destination. The traceroute command is one of the simplest yet most effective ways to troubleshoot network problems.

NOTE: 
- Different platforms offer traceroute as a tool, such as TRACERT on Windows or TRACEROUTE on Linux and Mac.
- Installation steps on both platforms are pretty simple, since we will be talking about Linux (Ubuntu). Below are the steps to get this utility installed.
- If you are on RHEL family, you can get this installed by `yum`.

```
$ apt-get update -y 
$ apt get install traceroute -y
```

Here are some examples of how to use the traceroute command in Linux:

To perform a traceroute to a host:
```
$ traceroute google.com
```

To perform a traceroute to a host with the IP address:
```
$ traceroute 142.250.77.78
```

To perform a traceroute with a maximum of 10 hops:
```
$ traceroute -m 10 google.com
```

To perform a traceroute with a maximum wait time of 2 seconds per hop:
```
$ traceroute -w 2 google.com
```

To perform a traceroute with only one packet sent per hop:
```
$ traceroute -q 1 google.com
```

Feel free to check its capabilities via `man traceroute`
