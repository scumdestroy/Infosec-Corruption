# API/SOAP/WSDL Tricks

* Classic SQL injection test / payload stuffing assault (i.e. `' or '1'='1 --`)
    - Trying to use a POST or JSON or even SOAP/XML request? You can copy-paste it from Burp into file, tag your intended injection point with a `*` and slap it into sqlmap or ghauri with the `-r muh_req.req` flag.
* Pop in some wildcards: `*` and `%`
* Throw in some empty strings or `'`, `"`, `=` or  `'`

Mess around with parameters like:
`_Login` then `login` then `login` then `login_`

* Try using a valid session or UUID, then mix it up with (other people's) correct, expired and faulty session IDs.  Note changes in server response time, minimal differences in response size or content and investigate deeper.

Quick login testing methodology run:
- Login, get sessionID
- logout
- make request while logged out w/ sssionID
- request with expired ID
- request with faulty sessionID
- how about two IDs log in simultaneously and send requests (parameter pollution?  race condition? almost anything you can do to a normal web app)

send

* Username and password
* just password
* just username
* username and username
* password and password
* nothing just &lt;login&gt; &lt;/logiN&gt;
* &lt;user\_name&gt; &lt;pass\_word&gt;
* &lt;user&gt; &lt;pass&gt;
* &lt;username&gt; &lt;pass&gt;
* &lt;user&gt; bigboy &lt;/user&gt;
* mypassword &lt;/password&gt;
* make payloads gigantic

**xpath injection:**

&lt;login&gt;  
  string\(//user\[username/text\(\)='' or '1' = '1' and password/text\(\)='' or '1' = '1'\]\)  
&lt;/login&gt;



Looking for BOLA \(IDOR\) in APIs? got 401/403 errors?

AuthZ bypass tricks:

* Wrap ID with an array {“id”:111} --&gt; {“id”:\[111\]}
* JSON wrap {“id”:111} --&gt; {“id”:{“id”:111}}
*  Send ID twice URL?id=&lt;LEGIT&gt;&id=&lt;VICTIM&gt;
*  Send wildcard {"user\_id":"\*"} 

