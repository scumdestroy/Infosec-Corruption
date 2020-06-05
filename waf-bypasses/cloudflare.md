---
description: >-
  Truly an adversary of mine, not by their cleverness and cunning, mostly by
  their endless numbers, seemingly haunting me at every turn..
---

# Cloudflare

**Finding a website's real IP address**

Finding the IP address of a website can help you in finding vulnerabilities, accessing accurate scans and other hidden goods that may not be as well protected, due to the expectation that the WAF will take care of any intruders by annoying them until they give up and move to another target.    
Some options you have at your disposal:  
  
This beautiful website keeps track of records sites may have had before signing up with cloudflare:  
 [http://crimeflare.org:82/cfs.html](http://crimeflare.org:82/cfs.html)  
  
You can also see DNS history on  
[netcraft.com](https://toolbox.netcraft.com)  
[viewdns.info](https://viewdns.info)  
[securitytrails.com](https://securitytrails.com)  
  
You can also do enumeration and recon on subdomains, MX records and other assets with simple tools like `dig` and `nslookup`

You can also try sending an e-mail to a fake address at the domain you are attending to uncover \(fakeyfakestrom8888888889@cloudydomain.com\) and the response you'll receive may have the IP details in it, if you examine all details from your e-mail or wireshark.  Though I think the outcome is the same as digging into MX records, and arguable noisier if you are trying to absolutely stay off of their radar.  



