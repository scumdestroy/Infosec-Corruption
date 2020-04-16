# API/SOAP/WSDL Tricks



* sql injection test
* wildcards : \* and %
* ' or 1=1--
* empty strings or ' or "='

_Login then login then login then login_

* try normal, then mix up correct, expired and faulty session IDs

Login, get sessionID

* logout
* make request while logged out w/ sssionID
* request with expired ID
* request with faulty sessionID
* how about two IDs log in simultaneously and send requests

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

