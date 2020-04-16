# HTTP Methods Vulns.

**PUT** : allows a user to store data or files on the server, if not applied carefully, an attacker can put malicious code on server

**DELETE :** delete important shit

**CONNECT :** another http method that usually isn't available to any users, can potentially allow a user to connect via a p2p tunnel connection, bypass IDS and upload/download sensitive files.

**HEAD :** although typically not seen as a vulnerable method, \(for example\) when the site uses java web.xml files for authorization, they may  only filter `GET` and `POST` requests, so `HEAD` can be used to bypass authorization. 

`nmap --script=http-methods.nse --script-args http-methods.retest=1` 

or you can manually send requests via netcat, telnet, postman, burp's repeater or any client that can access SOAP/REST.



