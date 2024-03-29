## netstat ##

Netstat is another powerful utility that is used to display network statistics about network connections, routing tables, and interfaces.

This is pretty useful in multiple scenarios, let's say for troubleshooting network issues (for example, port or connection issues) or to monitor network traffic and to detect malicious activity.

The syntax of the netstat command is as follows: netstat [options] [filter] [address]. Here, options can be used to specify certain parameters, such as which protocol to use, which ports to show, and which connection states to display. The filter parameter is used to filter the output based on specific criteria, such as the source or destination IP address. The address parameter is used to specify an IP address or port.

- To display all the active network connections:
```
$ netstat -a
```

- To display all the listening ports:
```
$ netstat -l
```

- To display all TCP ports:
```
$ netstat -at 
```

- To display all UDP ports:
```
$ netstat -au 
```

- To display kernal IP routing table: 
- PS: The kernel IP routing table, is also known as the IP routing table, which contains information about the network topology and routing paths on a Linux system.
```
$ netstat -r
```

- To display the interface table:
```
$ netstat -i
```

- To find a process that is using a particular port, in below example we are using `|` to fed the output of netstat to grep to filter out the results.
- -n is to get the IP addresses/numeric values (instead of domain names).
```
$ netstat -an | grep <PORT_NUMBER>
```

It is pretty powerful, feel free to check its capabilities via `man netstat`.
