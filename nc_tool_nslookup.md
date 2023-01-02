# nslookup #


This is the second tool on the list that is quite useful when it's necessary to determine whether a domain name or IP address can be resolved, or there can be other scenarios as well like:

- health checks.
- troubleshooting.
- sanity testing.
- put them in a pipeline.
- there could be N different use cases.


This article is nothing about new technologies or tools, but it's just a refresher on the utility called nslookup and what all it can do :)


In simple terms, it is a utility that queries domain name servers to determine whether or not a specific IP address or domain name is resolveable. Honestly speaking, it doesn't end just with the A records, you can get a wealth of information just from this tool.


Below, we will just go through some of the simple examples of how it can help and what information can we can get?


Though this command can be used in both modes, interactive and non-interactive, we will mostly look at the non-interactive side of it.


But still, if you are looking for the interactive one, just type `nslookup` and press Enter, and you will be brought to the nslookup prompt.


```
$ nslookup
> google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
Name: google.com
Address: 142.250.192.46
Name: google.com
Address: 2404:6800:4009:828::200e
>
```


Lets head on to some examples:


- If you are looking out for a simple DNS lookup, you can just fire `nslookup DOMAIN_NAME`:
```
$ nslookup google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
Name: google.com
Address: 142.250.192.46
Name: google.com
Address: 2404:6800:4009:828::200e
```


If you want to do a reverse DNS lookup, simply type `nslookup IP`:
```
$ nslookup 142.250.192.46
46.192.250.142.in-addr.arpa name = bom12s15-in-f14.1e100.net.
```


NOTE: You might see -type / -query / -querytype being used interchanbely.


- To find mail exchange servers, use `nslookup -type=mx DOMAIN_NAME`; honestly, this -type flag is really interesting and useful (you'll see some of the interesting things in the examples below).
```
$ nslookup -type=mx google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
google.com mail exchanger = 10 smtp.google.com.
```


- If you are looking for a list of name servers, you can use `nslookup -type=ns DOMAIN_NAME`:
```
$ nslookup -type=ns google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
google.com nameserver = ns1.google.com.
google.com nameserver = ns2.google.com.
google.com nameserver = ns3.google.com.
google.com nameserver = ns4.google.com.
```


- If you are looking for just the A records, use `a` with `-type`:
```
$ nslookup -type=a google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
Name: google.com
Address: 142.250.192.142
```


- The most interesting one is `any` with `-type`, which displays all the information that is available for a particular domain name:
```
$ nslookup -type=any google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
Name: google.com
Address: 216.58.196.78
Name: google.com
Address: 2404:6800:4009:809::200e
google.com rdata_257 = 0 issue "pki.goog"
google.com nameserver = ns2.google.com.
google.com text = "google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com text = "atlassian-domain-verification=5YjTmWmjI92ewqkx2oXmBaD60Td9zWon9r6eakvHX6B77zzkFQto8PQ9QsKnbf4I"
google.com text = "docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com mail exchanger = 10 smtp.google.com.
google.com rdata_65 = 1 . alpn="h2,h3"
google.com text = "docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com text = "onetrust-domain-verification=de01ed21f2fa4d8781cbc3ffb89cf4ef"
google.com nameserver = ns1.google.com.
google.com nameserver = ns3.google.com.
google.com text = "MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com text = "v=spf1 include:_spf.google.com ~all"
google.com
origin = ns1.google.com
mail addr = dns-admin.google.com
serial = 496879129
refresh = 900
retry = 900
expire = 1800
minimum = 60
google.com text = "apple-domain-verification=30afIBcvSuDV2PLX"
google.com text = "google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com nameserver = ns4.google.com.
google.com text = "facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
google.com text = "webexdomainverification.8YX6G=6e6922db-e3e6-4a36-904e-a805c28087fa"
google.com text = "globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
```


- If you're looking for authoritative information, you can use `soa` (start of authority; for more information, see https://en.wikipedia.org/wiki/SOA_record):
```
$ nslookup -type=soa google.com
Server: 127.0.0.53
Address: 127.0.0.53#53


Non-authoritative answer:
google.com
origin = ns1.google.com
mail addr = dns-admin.google.com
serial = 496879129
refresh = 900
retry = 900
expire = 1800
minimum = 60
```


- If you want to use any specific name server to fetch your results, you can use the name server as an argument in the command:
```
$ nslookup -type=a google.com ns3.google.com
Server: ns3.google.com
Address: 2001:4860:4802:36::a#53


Name: google.com
Address: 142.250.192.110
```


- If you want your result to time out after XYZ seconds, use `-timeout`, which can be used in any automation, such as a script.
```
$ nslookup -type=a -timeout=2 google.com ns3.google.com
Server: ns3.google.com
Address: 2001:4860:4802:36::a#53


Name: google.com
Address: 142.250.192.110
```
