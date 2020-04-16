# Intruder Payload Processing

Add prefix

Add suffix

Match / replace - replaces any part of payload \(based on regex\) with a string

Substring - extracts portion of payload, starting from a specified offset \(0-indexed\)

Reverse Substring - same as above, but counting backwards from end of the payload

Modify case - uppercase/lowercase

Encode - url, html, base64, ascii hex

Decode - url, html, base64, ascii hex

Hash - hashing operation on the payload

Add raw payload - to beginning or end of payload, useful if you need to submit hashed and non-hashed

Skip if matches regex - if matches a specific regex, skip to next one

Invoke burp extension - pre-configured jawns by extensions







* here are 18 types of payloads in intruder i.e.  

  * Simple list
  * Runtime File
  * Case Modification
  * Numbers
  * Character substitution
  * Custom iterator
  * Recursive grep
  * Illegal Unicode
  * Character blocks
  * Dates
  * Brute Forcer
  * Null Payloads
  * Character frober
  * Bit Flipper
  * Username generator
  * ECB block shuffler
  * Extension Generated
  * Copy other payload

  Simple list - obv

  Runtime file - configure a file which reads the payload strings at runtime.  Good for a large list of payloads, so it doesn’t have to hold the whole fuckign thing in memory and crash

  Case modification - make it uppercase, lowercase, no change, Proper Name, Proper name

  Numbers -

  Type : sequential or random generation

  From : first

  To : last \(highest number possible\)

  Step : if sequentially, whats the increment

  How many : total number of payloads generated \(random only\)

  Character substitution : what gets swapped for what

  JANN \( let's set a &gt; 5 , n &gt; y\) will create all of the following

  J5NN

  J5YN

  J5YY

  J5NY

  JAYN

  JANY

  JAYY

  Custom iterator :

  Recursive grep - based on what you're grepping for,will produce future payloads.  Like a server's error message may reveal a path to discover content. 

  -what to grep for \(and make payloads from\)

  -first payload

  -stop  if duplicate payloadfound

  * Illegal Unicode -variations to bypass WAFs vi acontinuation bytes, overlong encodings, similar looking characters
  * Character blocks -set to fuzz buffer overflow or logic flaws \(bypass mandatory length min/max in forms, end up at a strange path\)
  * Dates  set for brith date, wedding, anniversary, etc to brute force passwords
    * Parameters are From \(first date\) To \(last date\), step \(incrememnt for sequential - days, weeks, months, years\)  and format \(Mon v Monday, 2 v 02, 9 v 09 v 2009\)

  Brute Forcer

  Character set : set of characters to use

  Min length

  Max length

  * Null Payloads -  same ass payload over and over… for Denial of service, keeping session token alive or producing new cookies from same request
  * Character frober - a slow cycle thru the base string,one character at a time.  Used to test session token, to see if tiny changes have an effect or if it can be figured out
  * Bit Flipper - operate on : base value of payload position or another string.  Flip bits from least to most important \(ascii hex\)
  * Username generator  -  sets up a list of names or emails from a source, so "jan slezak" would produce janslezak, jan.slezak, slezakjan, slezak.jan, slezak, jan, jans
    * For when you target a user but don’t know their username
  * ECB block shuffler shitty passwords set up in blocks of 8 or 16 usually, so you can make a user like aaaaaaadmin and fuck with it to get thru authorization
  * Extension Generated - like from hackvertors encodings/decodings or XSS validator's phantomjs jawn
  * Copy other payload - copies other payload in same attack setup, like if you're using cluster bomb, pitchfork or battering ram
    * Useful when… need same payload types, sets, dictionaries, just different positions

