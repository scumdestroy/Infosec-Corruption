---
description: 'If the source code gets leaky, dig through the spaghetti a little.'
---

# PHP : grep Payloads

PHP Vulnerabilities could fill a library and will likely continue to fill evermore through the years of this language's livelihood on the web.  If you do get a chance to see the source code of a web app; be it open-source on github, a white-box test or just a poorly implemented leaky framework, here are some golden nuggets to grep for.

**XSS:**   
grep -Ri "echo" .   
grep -Ri "$_" . \| grep "echo"   
grep -Ri "$\_GET" . \| grep "echo"   
grep -Ri "$\_POST" . \| grep "echo"   
grep -Ri "$\_REQUEST" . \| grep "echo"   
  
**Command execution:**   
grep -Ri "shell\_exec\(" .   
grep -Ri "system\(" .   
grep -Ri "exec\(" .   
grep -Ri "popen\(" .   
grep -Ri "passthru\(" .   
grep -Ri "proc\_open\(" .   
grep -Ri "pcntl\_exec\(" .   
  
**Code execution:**   
grep -Ri "eval\(" .   
grep -Ri "assert\(" .   
grep -Ri "preg\_replace" . \| grep "/e"   
grep -Ri "create\_function\(" .   
  
**SQL Injection:**   
grep -Ri "$sql" .   
grep -Ri "$sql" . \| grep "$_"   
  
**SQLMAP Cheatsheet for WordPress:**   
`sqlmap -u "`[`http://target.tld/?paramater=1`](http://target.tld/?paramater=1)`" -p "parameter" --technique=B --dbms=mysql --suffix=")--" --string="Test" --sql-query="select user`_`login,user_pass from wp_users"`   
  
**Information leak via phpinfo:**   
grep -Ri "phpinfo" .   
  
**Find dev and debug modes:**   
grep -Ri "debug" .   
grep -Ri "$\_GET\['debug'\]" .   
grep -Ri "$\_GET\['test'\]" .   
  
**RFI/LFI:**   
grep -Ri "file\_include" .   
grep -Ri "include\(" .   
grep -Ri "require\(" .   
grep -Ri "require\($file\)" .   
grep -Ri "include\_once\(" .   
grep -Ri "require\_once\(" .   
grep -Ri "require\_once\(" . \| grep "$_"   
  
**Misc:**   
grep -Ri "header\(" . \| grep "$\_"   
grep -Ri '$\_SERVER\["HTTP\_USER\_AGENT"\]' .   
  
**Path Traversal:**   
grep -Ri file\_get\_contents .   
RATS Auditing tool for C, C++, Perl, PHP and Python

