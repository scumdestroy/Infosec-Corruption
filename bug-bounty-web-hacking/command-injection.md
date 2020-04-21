# Command Injection

When a web-app fails to sanitize an input and as a result, a user can attack the vulnerable web-app by sending unsafe arbitrary input, which is then forwarded as a shell command to the hosting server/system.   
Can potentially be discovered in any place a user's input is accepted, such as cookies, headers, form fields and more; though if the web-app is doing something that could easily be linked to a shell command \(such as ping or a SQL query\), it is more likely.

Like in the linux command line, there are multiple ways to add a second command.  
```````py  
CMD1 && CMD2  # run cmd1 and also cmd2  
CMD1 || CMD2  # run cmd2, if cmd1 fails  
CMD1 ; CMD2   # run cmd1, then cmd2  
CMD1 | CMD2   # send stdout of cmd1 to cmd2  
CMD1 & CMD2   # do both but only see stdout of CMD2  
CMD1 %0A CMD2 # execute both ```````

Some more ways to sneak by and inject some commands into a vulnerable shell  
`$  # can close regex   
;  # can terminate/end current cmd (SQL, python)`  


 





