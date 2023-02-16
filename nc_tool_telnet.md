## telnet ##

This article is about another network troubleshooting utility called `telnet`. It is a network protocol that allows you to connect to remote systems over a TCP/IP network. It is used to connect to servers and network equipment over port 23. Though telnet is not a secure protocol and is not recommended due to the lack of encryption, it really helps in troubleshooting issues.

- Telnet is simple to use; simply type `telnet` into the shell and you will be transported to the world of telnet.
```
$ telnet
telnet> quit
$
```

- To connect to a remote server:
```
$ telnet <remote server IP address>
```

- To check check if a remote port is open or not:
```
$ telnet <remote server IP address> <port number>
```

- To specifically connect to IPv4, use -4:
```
$ telnet -4 google.com 443
Trying 142.250.183.46...
Connected to google.com.
Escape character is '^]'.
^CConnection closed by foreign host.
$
```

- To specifically connect to IPv6, use -6:
$ telnet -6 google.com 443
Trying 2404:6800:4009:821::200e...
Connected to google.com.
Escape character is '^]'.
^CConnection closed by foreign host.
$
```

Feel free to check its `man` page for more details around telnet.
