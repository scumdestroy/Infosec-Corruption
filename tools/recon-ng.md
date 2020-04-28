---
description: "Vicious Reconnoisance Framework created by Tim Tomes (@lanmaster53) \U0001F50E"
---

# Recon-ng

> Comes standard with Kali Linux \(full\) or available here: https://github.com/lanmaster53/recon-ng  
> If not included in your version of Kali, the easiest and most up-to-date way to get it is via: **`apt update && apt install recon-ng`**

![Get it poppin&apos; by screaming its name \(lowercase scream\) in the terminal](../.gitbook/assets/recon-ng.JPG)

Since version 5, no modules come standard with recon-ng, so your first step will be to pull them all from the marketplace.  So to list available modules, run `marketplace search` . The syntax to use recon-ng is just different enough from Metasploit to elicit a couple grunts of frustration every time you open it and misfire a command.  However, to counterbalance this, it supports auto-completion with `[tab]`, unlike almost every tool available to the hacker.  

Some of the modules depend on API keys that are no longer free or may depend on a service that no longer exists, so you may be more picky with installing specific modules to avoid non-functional ones, but as an American, I like my options to bubble and overflow out of my terminal, regardless of their practicality.  Therefore, without regret or a second thought, my next command would be `marketplace install all`

{% hint style="warning" %}
Recently, I had a lot of errors and failures when trying to install all the modules, but I just ran it six times in a row and each time, a few more worked.  Try, try, try again until you succeed.  
{% endhint %}

You can type `help` to see all the options.   
Though 90% of the time, this is the flow of commands you'll be using: 

* modules search   \(to list all modules\)
* modules load &lt;module name&gt;
* options list 
* options set &lt;option parameter&gt; &lt;option value&gt; \(e.g. options set THREADS 16\)
* run

{% hint style="info" %}
🧙♂ **Tips:** option parameter names are case insensitive, though commands are, and are all set to lowercase.  _Suspiciously, parameter names are all in CAPS, though they'll work in any case you want._  
{% endhint %}

### **Categories for recon-ng modules**

🔎 **Discovery/info\_disclosure:** 🔎  
   **** _****Consists of "cache\_snoop" and "interesting\_files":_  **Cache snooping** is a technique where you can query a DNS server to see what requests have been made to it by its clients. This information can be further used to conduct a phishing campaign or a [cache poisoning attack](../bug-bounty-web-hacking/other-attacks.md).    
    The **interesting files** module does a fairly basic and quick scan of the domain for a handful of files that could potentially reveal information that could be further used in exploitation or just be sensitive in general.  The files it directly requests are:  `arerobots.txt, sitemap.xml, sitemap.xml.gz, crossdomain.xml, phpinfo.php, test.php, elmah.axd, server-status, jmx-console/, admin-console/, web-console/`  
as well as running through a few google dorks...

* inurl:elmah.axd ext:axd intitle:"Error log for"
* inurl:server-status "Apache Status"
* inurl:robots.txt ext:txt 

Ultimately, I'd prefer if you could set your own list of dorks and files, though there are loads of other tools that do this \([_wfuzz_](wfuzz.md)_, dirb, dirbuster, gobuster, ffuf_ for running through a files list, not as many for dorking, though they do exist\)

#### 💉 Exploitation by Injection modules 💉

    Unfortunately, I've never used either of these two modules, though not for a lack of trying but a lack of opportunity.  You'd have to be dealing with a pathetically vulnerable enemy, a good deal of luck or a server hiding in a time capsule for everything to line up beautifully enough for a KO from a recon-ng module, but I'm sure someone, somewhere has hit this jackpot.  Would be a nice feeling to be settling into your recon phase, crack your neck, blink your eyes and realize you've been catapulted directly into the post-exploitation stage.  If anyone knows of any write-ups that popped off either of these succesfully, please send them my way. 

#### Recon modules

**Recon/companies-\[contacts, domains, multi\]:** 

    ****These modules pull information about an organization by mining various web sources and parsing the information for you \(and storing it in the recon-ng database\).  Admittedly, you'll get more information doing a deep dive into this information manually, though these modules are great for their convenience.  You'll need API keys to make the most of the _Shodan_ and _Github_ modules, which can be found pretty with some dorking of pastebin and github, people readily give their private data out the internet more often than one would expect.    
`site:pastebin.com inbody:SHODAN_API` 

* _you can also try with pastebin alternatives or alternate ways of denoting the Shodan API syntax \(SHODANAPI, API\_SHODAN, apikey-shodan, etc..\)_ 

#### Recon/contacts-\[contacts, credentials, domains, profiles\]

    These modules  depend on having an e-mail of a target and performing various OSINT tasks, such as verifying the validity of the address or creating a wordlist based on a name or e-mail \(Burp's Intruder has this option, it turns a name like "Pig Man" into p.man, pigman, manpig, pig\_man, etc.. for use in brute forcing, cracking and enumerating usernames, passwords, services or anything you can think of\). The unmangle module allows you to apply any regex you can dream of to parse or modify an existing wordlist.  As of this writing, the hibp \([Have I been pwned](https://haveibeenpwned.com/)\) modules require an API that is no longer free but costs $3.50 a month.  You can still use the site to check individual e-mails \(maybe burp's turbo intruder could dance on it for you, just thought of it right now, haven't tested\) but ripping through a company's e-mail registry is a no-go without the three-fiddy.

#### Recon/credentials-credentials

    Can search hashes.org, google and the 2013 adobe breach for the credentials you are looking for.  Hashes.org requires an API for some heavy cred sifting.

#### Recon/domains=\[companies, contacts, credentials, domains, hosts, vulnerabilities\]

   This is where I do the majority of my recon-ng'ing.  Some pretty cool modules here that query services that I often forget to visit when doing my own manual recon process.  Some services, such as threatminer, builtwith, shodan and findsubdomains \(which I could write an encyclopedia about\) deserve further manual prodding and information gathering but there is definitely some good stuff here.    
      Modules that are no-brainer, always gonna grab this info type jawns:  


{% hint style="warning" %}
You will need an API key for the hunter\_io, binaryedge, pwnedlist, builtwith, threatmind, threatcrowd, shodan and \(possibly\) google modules
{% endhint %}

#### Recon/Domains, Hosts, Profiles

Most of these are the typical recon things you'd look for building up your phase prior to pentesting or doing a bug-bounty excursion.  Some of the github stuff is nice but perhaps better accomplished by the more recent gitgrabbers, trufflehogs and the like.  Nothing beats going through it manually though, as you'll find subdomains, endpoints and other things that a scanner will not likely notice.  I think the bing API is kinda a bust at this point \(or maybe just costs money and has been largely abandoned\).  Virustotal and censys are great sources but I also prefer to check them personally.

                       
**Shodan modules:**  
    I'm a huge fan of [Shodan](https://shodan.io), especially the website and the way its changed my recon process and elicited wonder throughout the many glimpses of the world it can provide.  It is especially trill if you have a paid account, as many more options open up \(but definitely get a community account at least and drop your API key into recon-ng\).    
The shodan-related modules in the recon-ng marketplace are:

```text
recon/companies-multi/shodan_org
recon/domains-hosts/shodan_hostname
recon/hosts-ports/shodan_ip
recon/locations-pushpins/shodan
recon/netblocks-hosts/shodan_net
```

Though beyond the scope of recon-ng, I believe to effectively use Shodan, you must be familiar with using it from the command line.  Even with a community account, you can access a significant amount more data by using this over using the web interface.   
  
You can get it easily via:   
`pip3 install shodan`  _and configure it with_ `shodan init <API KEY>`

Though likely not relevant to your recon process for your pen-testing engagement or your bug-bounty campaign \(though you never know\), these links and the people behind them have my full support and appreciation.  

{% embed url="https://github.com/humblelad/Shodan-Dorks" %}



\*\*\*\*



\`\`



