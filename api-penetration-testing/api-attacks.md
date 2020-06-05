# API attacks



* access controls - authorization/authentication
  * basically, proving who you say you are / granting access to resources

the server process for auth. checks goes like..

* if account/session exists
* if request/resource is in access scope of client
* if any cookies/tokens needed
* if the way to route to the resource was valid
* if any other conditions the server expects are met

measuring success : if server returns a token/session id, further requests will be granted

how to test for access controls?

* enumerate potential restricted endpoints
* modify session tokens
* attempt to bypass restrictions with IDOR
* modify request with parameters like "&admin=true"  or "test=1"
* modify referrer headers to things the app will expect \(say you came from some other part of the app\)

**INPUT VALIDATION**

* input is ANYTHING a server takes in : from user, third party apps, elsewhere

**parameters to test::**

* within request header
* parameters within the URL
* parameters in the request
* file uploads \(PUT/DELETE requests\)
* input validation bugs :: -improper parameterization of requests in logic
* lack of input sanitation/escaping
* improper handling of parameters
* file upload bugs, unicode bugs \(what can you pass into server for data and how is it handled\)

**input validation fuzzing**

* RCE, XSS, LFI/RFI, SQL injection, request splitting, deserialization, XXE, encoding errors with junk characters/emojis, file upload vulnerabilities, SSRF
* you can use burp, gobuster, dirb, etc

  
**Rate limiting - ways to test**

* make requests in varying states of authentication
* as a unauthenticated user
* a legit ones
* as a developer
* as a bot
* with a deactivated account
* with bogus credentials

you can make a gross amount of requests and enumerate or find DDOS ways

**Restricting HTTP methods**

* what the app supports and what it expects is important before fuzzing
* sometimes scope of methods is too broad leading a user to be able to PUT DELETE POST etc parts of the API
* even if they aren't included, an app can be lazy and have nothing to parse and will do weird shit

**3rd party API abuse - sometimes APIs rely on other APIs**

* there is usually trust between the two
* request splitting - making additional requests to a third party API thru target API
* SSRF - APIs that resolve URLs can be tricked into making requests to the server itself \(to gain data, enumeration, cloud access\)
* mishandled input from 3rd party

Information Disclosure

How to find it:

* use wappalyzer and look for ruby and angular or ruby/react combo
* These usually follow pattern of /model/id or model/subresource/id

In proxy history ::

* Look for giant JSON encoded blobs in page sources
* Watch for API calls



