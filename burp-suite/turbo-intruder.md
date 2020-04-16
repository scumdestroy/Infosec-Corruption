# Turbo Intruder

highlight what you want to fuzz and send to turbo

customize the python code that opens up if you want

request area replaced with %s \(payloads will be placed here\)

filtering boring results

example:

_`def handleResponse(req,  interesting);`_

_`if '200 oOK' in req.response:`_

_`table.add(req)`_

this strategy is used by default script

**speed tuning:**

`pipeline`

`requestsPerConnection`

`concurrentConnections`

the goal is to get your retries counter close to 0

-can go up inspeed by changing **GET** to **HEAD** or changing size of request

**finding race conditions** - default mode isn't great for this, to find a race condition,  you'll want to ensure your requests hit the target in as small of a window as possible, which you can do by queuing your requests before starting the request engine, turbo intruder comes with an example of this \(race.py\)

It also comes with...

* special/wordlists.py\(2 of them\)
* one super long bruteforcer
* one that containes all words observed in inscope proxy traffic

