---
description: >-
  Oftentimes, bounty hunters need to get across the impact of the vulnerability,
  sometimes to those with less technical understanding than us.  Some concepts
  to guide your testing and frame your report
---

# Application & Business Logic

* Identify the logic attack surface What does the application do, what is the most value, what would an attacker want to access?
* Test transmission of data via the client Is there a desktop application or mobile application, does the transferal of information vary between this and the web application
* Test for reliance on client-side input validation Does the application attempt to base it's logic on the client side, for example do forms have a maximum length client side that can be edited with the browser that are simply accepted as true?
* Test any thick-client components \(Java, ActiveX, Flash\) Does the application utilize something like Java, Flash, ActiveX or silverlight? can you download the applet and reverse engineer it?
* Test multi-stage processes for logic flaws Can you go from placing an order straight to delivery thus bypassing payment? or a similar process?
* Test handling of incomplete input Can you pass the application dodgy input and does it process it as normal, this can point to other issues such as RCE & XSS.
* Test trust boundaries What is a user trusted to do, can they access admin aspects of the app?
* Test transaction logic
* Can you pay £0.00 for an item that should be £1,000,000 etc?
* Test for Insecure direct object references\(IDOR\)
* Can you increment through items, users. uuids or other sensitive info?



