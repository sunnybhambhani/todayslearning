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
- Get the HTTP status code and make your pipeline more meaningful based on the HTTP statusCode and its respective conditions.
```
$ curl -s -o /dev/null -w %{http_code} https://google.com
301
```
- You can even use other HTTP methods like POST, DELETE, etc. with curl; we just need to specify --request or -X and the HTTP method name [the default method is GET].
```
$ curl -X GET https://google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
```
- You can even pass data using --data and --data-binary using -X POST method.
- Frequently used flags:
```
-v: In case you wanted to do verbose logging, you could use this flag.
-s: In case you don't want anything to be shared over STDOUT, you can use the -s or --slient flag.
-o: Sending the output to a file or /dev/null
-X: If you want to pass any HTTP method.
-I: In case you want to just fetch the headers, this can come handy.
-w: Or --write-out helps to display a specific information after a request is successfully completed. Like http_code, http_version, content_type, etc.
-k: In the case of https, this can be used to bypass certificate requirements.
-u: In case the endpoint requires credentials, this can be used to pass in the username and password.
```
<br><br>
There are many other flags that can be used based on an individual's requirements. For more information, you can fire `$man curl` and get a gist of what all it can do.
