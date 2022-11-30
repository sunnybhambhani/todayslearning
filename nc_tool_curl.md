# curl #

There might be scenarios where one needs to check the reachability of servers or the network connectivity, say for doing:
- health checks.
- troubleshooting.
- include them in a pipeline.
- there can be N number of use cases.

Curl really stands out and is a pretty popular utility.
</br>
</br>


This article is nothing about new technologies or tools, but it's just a refresher on the utility called curl and what all it can do :)
</br>
</br>


**curl:**
- This is an all-purpose tool that supports N number of protocols, and you can do a lot with it. Below are some common examples of where it can be used:


- Simple GET request:
```
$ curl https://google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
```
- Get the headers:
```
$ curl -I https://google.com
HTTP/2 301
location: https://www.google.com/
content-type: text/html; charset=UTF-8
cross-origin-opener-policy-report-only: same-origin-allow-popups; report-to="gws"
report-to: {"group":"gws","max_age":2592000,"endpoints":[{"url":"https://csp.withgoogle.com/csp/report-to/gws/other"}]}
date: Wed, 30 Nov 2022 06:22:01 GMT
expires: Fri, 30 Dec 2022 06:22:01 GMT
cache-control: public, max-age=2592000
server: gws
content-length: 220
x-xss-protection: 0
x-frame-options: SAMEORIGIN
alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000,h3-Q050=":443"; ma=2592000,h3-Q046=":443"; ma=2592000,h3-Q043=":443"; ma=2592000,quic=":443"; ma=2592000; v="46,43"
```
