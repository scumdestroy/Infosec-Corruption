---
description: Server Side Template Injection
---

# SSTI

Another vulnerability that falls victim to flawed input sanitization.  Though not as common as XSS or SQLI, due to the fact that the functionality of providing a template engine that renders static template files, replacing the built-in variables with actual values is not as common as a basic user input, or reliance on back-end database.  In addition, there are generally only certain frameworks that are vulnerable, though a successful attack on these can lead from private information disclosure to a full on RCE. 

Generally pretty simple to test for and understand if the vulnerability is present.  Just drop a payload like `${{7*7}}` or `#${7*7}` and if the web app parses it into a 49, the bounty gates will open for you and dazzle you with riches \(hopefully\).     
_Also, you can of course test for XSS, SQLI and any other input madness of interest to you._

