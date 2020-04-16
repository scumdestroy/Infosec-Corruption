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

**AUTHENTICATION**

* Check for basic auth
* Test max retry and jail features in login
* Sensitive data not encrypted?

**JWT**

* Test brute-forcing w/ jwt\_tool
* Test if algorhythm can be changed \(jwt.io\)
* Test token expiration \(TTL, RTTL\)
* Jwt.io to test if sensitive data in token
* Injection possible in 'kid' element
* Check for time constant verification for HMAC
* Check that keys and secrerts are different between ENV

**Oauth**

* Test redirect\_uri for open redirect
* Test existence of response tpye token
* Test CSR

**Access**

\*test brute force

Ind http requests

Test lack o HSTS header

**INPUT**

* Test http methods \(get, put, delete, patch, post\)
* Test different content types
* Test for common vulns \(XXS&lt; SQLI, RCE, XXE, etc\)
* Test or url sensitive data \(password, tkens, api keys\)

**Processing**

* check if all endpoints are protected behind auth
* Check if resource iD is used in the urlk
* Test for debug use

**Output**

* Check for X-content-type options nosniff
* X-frame options : deny
* Content security policy default src none
* Check for fingerprinting headers \(x-owered by, server, x-aspnet version\)
* Check for content type forcing
* Check for return sensitive data

