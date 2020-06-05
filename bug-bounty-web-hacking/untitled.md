---
description: >-
  Find any and all UIDs ● increment ● decrement ● negative values ● Attempt to
  perform sensitive functions substituting another UID ○ change password ○
  forgot password ○ admin only functionswh
---

# IDORs / Auth. Bugs

**User IDs**   can they be predicted? altered? created? found somewhere else in the app \(URL of user's profile, some other section of the site\)

 `GET /api_v1/messages?conversation_id=`**SOME\_RANDOM\_ID** ``

Find any and all UIDs and manipulate them.

* increment them
* decrement them
*  substitute with negative values 
* try various naughty strings such as... 
* non UTF-8 characters, "null/undefined", 0.00, $0.0, 0.0.0, 1.0/0.0, -,,  
   !@\#$%^&\*\(\)\`~,

  ­؀؁؂؃؄؅؜۝܏᠎​‌‍‎‏‪‫‬‭‮⁠⁡⁢⁣⁤⁦⁧⁨⁩⁪⁫⁬⁭⁮⁯  
   ￹￺￻𑂽𛲠𛲡𛲢𛲣𝅳𝅴𝅵𝅶𝅷𝅸𝅹𝅺󐀁󐀠󐀡󐀢󐀣󐀤󐀥󐀦󐀧󐀨󐀩󐀪󐀫󐀬󐀭󐀮󐀯󐀰󐀱󐀲󐀳󐀴󐀵󐀶󐀷󐀸󐀹󐀺󐀻󐀼󐀽󐀾󐀿󐁀󐁁󐁂󐁃󐁄󐁅󐁆󐁇󐁈󐁉󐁊󐁋󐁌󐁍󐁎󐁏󐁐󐁑󐁒󐁓󐁔󐁕󐁖󐁗󐁘󐁙󐁚󐁛󐁜󐁝󐁞󐁟󐁠󐁡󐁢󐁣󐁤󐁥󐁦󐁧󐁨󐁩󐁪󐁫󐁬󐁭󐁮󐁯󐁰󐁱󐁲󐁳󐁴󐁵󐁶󐁷󐁸󐁹󐁺󐁻󐁼󐁽󐁾󐁿  
  , ., ¸˛Ç◊ı˜Â¯˘¿  
  , 部落格  
    ❤️ 💔 💌 💕 💞  
  , ﷽

* src=JaVaSCript:prompt\(132\), eval\("puts 'hello world'"\), File:///, 🏳0🌈️, {0}, $USER

Try to substitute other used IDs, hashes, e-mails or anything else belonging to a user that is non-public, if you can't guess them, create another account and pull from there.   

For greater business impact, it is better to target more important functions rather than less significant ones such as "change e-mail subscription settings".  A few sensitive functions to prioritize your vulnerability hunting on are:

*  forgot password 
* change password 
* admin only functions
* account recovery functions
* functions handling direct messaging
* private content and user information 

Some sensitive items you can try to access while unauthorized.

* Receipts
* Private files \(PDFs, images\)
* Shipping info and purchase orders

Even if the app doesn't ask for it, try appending `id` ,`user_id` ,`message_id` or other parameters to the request.

**Privilege Bugs**

Find functions where different types of users are granted different abilities \(admin vs. manager vs. regular user\) and try to use abilities granted only to power users.   Try to browse to areas that are restricted from a regular user by directly inputting the URL from the power user account. Autorize, Authz and Auto-repeater can all help in Burp Suite.   

**Common Privilege Bug Functions**

* Add user function
* Delete user function
* start project / campaign / etc function
* change account info \(pass, CC, etc\) function
* customer analytics view
* payment processing view
* any page where you can view PII

**Transporting Data**

Most security concerned sites will enable HTTPs, though not perfectly across every page.  Often times they miss something but only report pages that contain any sensitive information.

Examples:

* Sensitive images transported over HTTP
* Analytics with session data / PII leaked over HTTP

**Business logic flaws**

The following vulnerabilities cannot typically be found with scanners and require manual testing to find.

* substituting hashed parameters
* step manipulation
* use negatives in quantities
* authentication bypass
* application level DoS
* Timing attacks

**Insecure Data Storage Methods**

Its common to see mobile apps not applying encryption to the files that store PII.

Common places to find PII unencrypted

* Phone system logs \(available to all apps\)
* webkit cache \(cache.db\)
* plists, dbs, etc.
* hardcoding data in the binary



