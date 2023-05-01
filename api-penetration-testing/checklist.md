# Checklist

1\) Observe each parameter in every module of API, understand how the data is transferred from source to destination. Try to play with the parameter by tampering them.

2\) Identify if the API has any authorization token if it is having then remove that authorization token and see application response. In some cases, if authorization is not implemented correctly then API might give you access to forbidden assets of application.

3\) Analyze and check each module with a different access level of user ex: admin, moderator, normal user.

4\) Check whether admin modules can be accessed via the restricted user.

5\) Identify the parameters which may vulnerable to IDOR type vulnerabilities such as id=1234 and also look at the cookies for manipulating the Ids.

6\) Check injection vulnerabilities by inserting special characters in all parameters in a request and check the response from the server. If you find any stack traces then use the information for further exploitation.

7\) Insert greater than, less than \(&lt;,&gt;\) characters in all parameters and see response whether the application encoding them as &gt; and &lt;. If an application doesn’t escape any special characters then the application may be vulnerable to client-side attacks such as XSS \(cross-site scripting\).

8\) Modify the content-type server header for understanding the XML entity injection attack. Example: change content Application/JSON to application/XML and insert the XML entity payload to find the XML entity injection.

**most popular tools for testing REST and APIs**

-postman

-soapui

-restAssured

-httpclient  api automation

-Burp Suite/ZAP, of course.

**AUTHENTICATION**

* Check for basic auth and fuzz "admin:admin", "test:test", "guest:<organization name>" or default creds after fingerprinting your target.  You can set up intruder to take a list of usernames, a list of passwords, insert a `:` in between, encode them with base64 and plug them into the `Authorization: Basic $FUZZ$` header
* Test rate limiting and lockout features in login
* Sensitive data not encrypted or using unsufficient crypto (i.e. MD5 or base64 + MD5, check out code chef or the Decode tool)

### **JWT**

* Test brute-forcing w/ jwt\_tool
* Test if algorhythm can be changed \(jwt.io or Burp\)
* Test token expiration \(TTL, RTTL\)
* Jwt.io to easily decrypt and test if sensitive data in token
* Injection possible in 'kid' element
* Check for time constant verification for HMAC
* Check that keys and secrerts are different between ENV

### **Oauth**

* Test redirect\_uri for open redirect
* Test existence of response type token
* Test CSR
* Honestly, I need to brush up on here, there's lots of research out there to learn

Test lack of HSTS header

### **INPUT**

* Test http methods \(GET, POST, PUT, PHATFARM, DELETE\)
* Test different content types
* Test for common vulns \(XXS&lt; SQLI, RCE, XXE, etc\)
* Test for sensitive data in URLs, JS files, etc... \(password, tokens, api keys\)

### **Processing**

* check if all endpoints are protected behind auth
* Check if resource iD is used in the urlk
* Test for debug use

### **Output**

* `X-Frame-Options: Deny`
* Content security policy default src none
* Check for fingerprinting headers \(X-Powered-By, Server, X-ASPnet-Version\)
* Check for content-type forcing
* Check for sensitive data in response

