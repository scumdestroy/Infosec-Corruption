---
description: >-
  This challenge is Inon Shkedy's 31 days API Security Tips 
  https://twitter.com/inonshkedy
---

# 31 days of API security tricks

-API TIP: 1/31-

Older APIs versions tend to be more vulnerable and they lack security mechanisms. Leverage the predictable nature of REST APIs to find old versions. Saw a call to api/v3/login? Check if api/v1/login exists as well. It might be more vulnerable.

-API TIP: 2/31-

Never assume there’s only one way to authenticate to an API! Modern apps have many API endpoints for AuthN: /api/mobile/login \| /api/v3/login \| /api/magic\_link; etc.. Find and test all of them for AuthN problems.

-API TIP:3/31-

Remember how SQL Injections used to be extremely common 5-10 years ago, and you could break into almost every company? BOLA \(IDOR\) is the new epidemic of API security. As a pentester, if you understand how to exploit it, your glory is guaranteed.

Learn more about BOLA : [https://medium.com/@inonst/a-deep-dive-on-the-most-critical-api-vulnerability-bola-1342224ec3f2](https://medium.com/@inonst/a-deep-dive-on-the-most-critical-api-vulnerability-bola-1342224ec3f2)

-API TIP: 4/31-

Testing a Ruby on Rails App & noticed an HTTP parameter containing a URL? Developers sometimes use "Kernel\#open" function to access URLs == Game Over. Just send a pipe as the first character and then a shell command \(Command Injection by design\)

Learn more about the open function: [https://apidock.com/ruby/Kernel/open](https://apidock.com/ruby/Kernel/open)

-API TIP:5/31-

Found SSRF? use it for:

* Internal port scanning
* Leverage cloud services\(like 169.254.169.254\)
* Use [http://webhook.site](http://webhook.site/) to reveal IP Address & HTTP Library
* Download a very large file \(Layer 7 DoS\)
* Reflective SSRF? disclose local mgmt consoles

-API TIP: 6/31-

Mass Assignment is a real thing. Modern frameworks encourage developers to use MA without understanding the security implications. During exploitation, don't guess object's properties names, simply find a GET endpoint that returns all of them. 

[![Infographic](api_files/image001.jpg)](https://camo.githubusercontent.com/c9590bb2d38418b08741940544a235dcb8e6c8f4/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f454e7073573235585941416a454a453f666f726d61743d6a7067)

- API TIP: 7/31 -

A company exposes an API for developers? This is not the same API which is used by mobile / web application. Always test them separately. Don't assume they implement the same security mechanisms.

- API TIP: 8/31 -

Pentest for REST API? Give it a chance and check if the API supports SOAP also. Change the content-type to "application/xml", add a simple XML in the request body, and see how the API handles it.

Sometimes the authentication is done in a different component that is shared between REST & SOAP APIs == SOAP API may support JWT

If the API returns stack trace with a DUMPling, it's probably vulnerable\*\*

- API TIP: 9/31 -

Pentest for APIs? Trying to find BOLA \(IDOR\) vulnerabilities? IDs in the HTTP bodies/headers tend to be more vulnerable than IDs in URLs. Try to focus on them first.

-API TIP: 10/31-

Exploiting BFLA \(Broken Function Level Authorization\)? Leverage the predictable nature of REST to find admin API endpoints! E.g: you saw the following API call GET /api/v1/users/&lt;id&gt; Give it a chance and change to DELETE / POST to create/delete users.

- API TIP: 11/31 -

The API uses Authorization header? Forget about CSRF! If the authentication mechanism doesn't support cookies, the API is protected against CSRF by design.

-API TIP : 12/31-

Testing for BOLA \(IDOR\)? Even if the ID is GUID or non-numeric, try to send a numeric value. For example: /?user\_id=111 instead of user\_id=inon@traceable.ai Sometimes the AuthZ mechanism supports both and it's easier the brute force numbers.

-API TIP: 13/31-

\*Use Mass Assignment to bypass security mechanisms. E.g., "enter password" mechanism:

* POST /api/reset\_pass requires old password.
* PUT /api/update\_user is vulnerable to MA == can be used to update pass without sending the old one \(For CSRF\)\*

- API TIP: 14/31 -

Got stuck during an API pentest? Expand your attack surface! Find sub/sibling domains using [http://Virustotal.com](http://virustotal.com/) & [http://Censys.io](http://censys.io/). Some of these domains might expose the same APIs with different configurations/versions.

-API TIP:15/31-

Static resource==photo,video,.. Web Servers\(IIS, Apache\) treat static resources differently when it comes to authorization. Even if developers implemented decent authorization, there's a good chance you can access static resources of other users.

-API TIP: 16/31-

Even if you use another web proxy, always use Burp in the background. The guys at @PortSwigger are doing a really good job at helping you manage your pentest. Use the “tree view” \(free version\) feature to see all API endpoints you’ve accessed.

-API TIP:17/31-

Mobile Certificate Pinning? Before you start reverse engineering & patching the client app, check for both iOS & Android clients and older versions of them. There's a decent chance that the pinning isn't enabled in one of them. Save time.

-API TIP: 18/31-

Companies & developers tend to put more resources \(including security\) into the main APIs. Always look for the most niche features that nobody uses to find interesting vulnerabilities. POST /api/profile/upload\_christmas\_voice\_greeting

-API TIP:19/31-

Which features do you find tend to be more vulnerable? I'll start:

* Organization's user management
* Export to CSV/HTML/PDF
* Custom views of dashboards
* Sub user creation&management
* Object sharing \(photos, posts,etc\)

- API TIP:20/31-

Testing AuthN APIs? If you test in production, there's a good chance that AuthN endpoints have anti brute-force protection. Anyhow, DevOps engineers tend to disable rate limiting in non-production environments. Don't forget to test them :\)

A good example of this issue: Facebook Breach \(Found by @sehacure\) [http://www.anandpraka.sh/2016/03/how-i-could-have-hacked-your-facebook.html](http://www.anandpraka.sh/2016/03/how-i-could-have-hacked-your-facebook.html)

-API TIP:21/30-

Got stuck during an API pentest? Expand the attack surface! Use [http://archive.com](http://archive.com/), find old versions of the web-app and explore new API endpoints. Can't use the client? scan the .js files for URLs. Some of them are API endpoints.

-API TIP:22/31-

APIs tend to leak PII by design. BE engineers return raw JSON objects and rely on FE engineers to filter out sensitive data. Found a sensitive resource \(e.g, receipt\)? Find all the EPs that return it: /download\_receipt,/export\_receipt, etc..

Some of the endpoints might leak excessive data that should not be accessible by the user.

This is an example for OWASP Top 10 For APIs - \#3 - Excessive Data Exposure

-API TIP:23/31-

Found a way to download arbitrary files from a web server? Shift the test from black-box to white-box. Download the source code of the app \(DLL files: use IL-spy; Compiled Java - use Luyten\) Read the code and find new issues!

-API TIP:24/31-

Got stuck during an API pentest? Expand your attack surface! Remember: developers often disable security mechanisms in non-production environments \(qa/staging/etc\); Leverage this fact to bypass AuthZ, AuthN, rate limiting & input validation.

-API TIP:25/31-

Found an "export to PDF" feature? There's a good chance the developers use an external library to convert HTML --&gt; PDF behind the scenes. Try to inject HTML elements and cause "Export Injection".

Learn more about Export Injection: [https://medium.com/@inonst/export-injection-2eebc4f17117](https://medium.com/@inonst/export-injection-2eebc4f17117)

-API TIP:26/31-

Looking for BOLA \(IDOR\) in APIs? got 401/403 errors? AuthZ bypass tricks:

* Wrap ID with an array {“id”:111} --&gt; {“id”:\[111\]}
* JSON wrap {“id”:111} --&gt; {“id”:{“id”:111}}
* Send ID twice URL?id=&lt;LEGIT&gt;&id=&lt;VICTIM&gt;
* Send wildcard {"user\_id":"\*"}

In some cases, the AuthZ mechanism expects a plain string \(an ID in this case\), and if it receives a JSON instead it won't perform the AuthZ checks. Then, when the input goes to the data fetching component, it might be okay with a JSON instead of string\(e.g: it flattens the JSON\)

-API TIP:27/31-

BE Servers no longer responsible for protecting against XSS. APIs don't return HTML, but JSON instead. If API returns XSS payload? - E.g: {"name":"In&lt;script&gt;alert\(21\)&lt;/script&gt;on} That's fine! The protection always needs to be on the client side

-API TIP:28/31-

Pentest for .NET apps? Found a param containing file path/name? Developers sometimes use "Path.Combine\(path\_1,path\_2\)" to create full path. Path.Combine has weird behavior: if param\#2 is absolute path, then param\#1 is ignored.

Leverage it to control the path

Learn more: [https://www.praetorian.com/blog/pathcombine-security-issues-in-aspnet-applications](https://www.praetorian.com/blog/pathcombine-security-issues-in-aspnet-applications)

-API TIP:29/30-

APIs expose the underlying implementation of the app. Pentesters should leverage this fact to better understand users, roles, resources & correlations between them and find cool vulnerabilities & exploits. Always be curious about the API responses.

-API TIP:30/31-

Got stuck during an API pentest? Expand your attack surface! If the API has mobile clients, download old versions of the APK file to explore old/legacy functionality and discover new API endpoints.

Remember: companies don’t always implement security mechanisms from day one && DevOps engineers don’t often deprecate old APIs. Leverage these facts to find shadow API endpoints that don’t implement security mechanism \(authorization, input filtering & rate limiting\)

Download old APK versions of android apps: [https://apkpure.com](https://apkpure.com/)

-API TIP: 31/31-

Found a limit / page param? \(e.g: /api/news?limit=100\) It might be vulnerable to Layer 7 DoS. Try to send a long value \(e.g: limit=999999999\) and see what happens :\)

Source

All of this information is taken from twitter of Inon Shkedy

Links:

* [Inon Shkedy](https://twitter.com/inonshkedy)
* [Traceableai](https://twitter.com/traceableai/)
* [OWASP API PROJECT](https://github.com/OWASP/API-Security)

