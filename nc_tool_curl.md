# curl #

There might be scenarios where one needs to check the reachability of servers or the network connectivity, say for doing:
- health checks.
- troubleshooting.
- sanity testing.
- include them in a pipeline.
- mimicking a request. 
- there can be N number of use cases.

</br>


This article is nothing about new technologies or tools, but it's just a refresher on the utility called curl and what all it can do. Curl really stands out and is a pretty popular utility :)

This is an all-purpose tool that supports N number of protocols, and you can do a lot with it.

</br>


**Below are some of the usecases:**
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
- Get just the headers:
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
- Get the HTTP status code and make your pipeline more meaningful based on the HTTP statusCode and their respective conditions. You can get N number of values out of it like http_code, http_version, content_type, etc. See its man page for more details `$ man curl`.
  - Instead of using awk/cut/grep/etc, this can come really handy.
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
- If you want to follow the redirects -L can come handy.
```
$ curl -L google.com 
<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="en-IN"><head><meta content="text/html; charset=UTF-8" http-equiv="Content-Type"><meta content="/logos/doodles/2022/jerry-lawsons-82nd-birthday-6753651837109552.3-l.png" itemprop="image"><meta content='Gerald "Jerry" Lawson&#39;s 82nd Birthday' property="twitter:title"><meta content='Build your own game by clicking today&#39;s #GoogleDoodle celebrating the pioneer of the video game cartridge: Gerald "Jerry" Lawson! ' property="twitter:description"><meta content='Build your own game by clicking today&#39;s #GoogleDoodle celebrating the pioneer of the video game cartridge: Gerald "Jerry" Lawson! ' property="og:description"><meta content="summary_large_image" property="twitter:card"><meta content="@GoogleDoodles" property="twitter:site"><meta content="https://www.google.com/logos/doodles/2022/jerry-lawsons-82nd-birthday-6753651837109552-2xa.gif" property="twitter:image"><meta content="https://www.google.com/logos/doodles/2022/jerry-lawsons-82nd-birthday-6753651837109552-2xa.gif" property="og:image"><meta content="1064" property="og:image:width"><meta content="400" property="og:image:height"><meta content="https://www.google.com/logos/doodles/2022/jerry-lawsons-82nd-birthday-6753651837109552-2xa.gif" property="og:url"><meta content="video.other" property="og:type"><title>Google</title><script nonce="NLrwV4-tMqHq9_kiTUbHoA">(function(){window.google={kEI:'GbOIY9bgAb_hkPIP_Je
.
.
Output trimmed
```
- If you want to have more detailed information about the request, use -v or --verbose
```
$ curl -v google.com 
*   Trying 2404:6800:4009:82e::200e:80...
* Connected to google.com (2404:6800:4009:82e::200e) port 80 (#0)
> GET / HTTP/1.1
> Host: google.com
> User-Agent: curl/7.81.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< Location: http://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Cross-Origin-Opener-Policy-Report-Only: same-origin-allow-popups; report-to="gws"
< Report-To: {"group":"gws","max_age":2592000,"endpoints":[{"url":"https://csp.withgoogle.com/csp/report-to/gws/other"}]}
< Date: Thu, 01 Dec 2022 14:01:06 GMT
< Expires: Sat, 31 Dec 2022 14:01:06 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 219
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< 
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com left intac
```
- In some cases you might want to simulate the request is coming from an intended brower instead of curl in such cases -A can come handy. Check below output confirming the request coming in from intended user-agent.
```
$ curl -v -A "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36" google.com 
*   Trying 142.251.12.101:80...
* TCP_NODELAY set
* Connected to google.com (142.251.12.101) port 80 (#0)
> GET / HTTP/1.1
> Host: google.com
> User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 301 Moved Permanently
< Location: http://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Cross-Origin-Opener-Policy-Report-Only: same-origin-allow-popups; report-to="gws"
< Report-To: {"group":"gws","max_age":2592000,"endpoints":[{"url":"https://csp.withgoogle.com/csp/report-to/gws/other"}]}
< BFCache-Opt-In: unload
< Date: Thu, 01 Dec 2022 15:41:02 GMT
< Expires: Sat, 31 Dec 2022 15:41:02 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 219
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< 
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com left intact
```
- You can send a request to an endpoint with TLS enabled using --cert / --key / --cacert flags or you can even bypass the certificate requirements using -k (or --insecure) flag. 
```
$ curl -k https://HTTPS_ENDPOINT
```
- You can even pass data using --data and --data-binary using -X POST method.
- Frequently used flags:
```
-v: In case you wanted to do verbose logging, you could use this flag ( or --verbose).
-s: In case you don't want anything to be shared over STDOUT, you can use the -s (or --slient) flag.
-o: Sending the output to a file or /dev/null (or --output).
-X: If you want to pass any HTTP method (or --request).
-H: If any extra header is required to be sent in the request, -H (or --header) can come handy.
-I: In case you want to just fetch the headers, this can come handy.
-w: (or --write-out) helps to display a specific information after a request is successfully completed. Like http_code, http_version, content_type, etc.
-k: In the case of https, this can be used to bypass certificate requirements (or --insecure).
-u: In case the endpoint requires credentials, this can be used to pass in the username and password (or --user).
-A: Simulate a request from custom useragent (or --user-agent).
```
<br><br>
There are many other flags that can be used based on an individual's requirements. For more information, you can fire `$ man curl` and get a gist of what all magic it can do.
