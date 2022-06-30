Before we get into the practical side of things, let's talk a little about the different detection methods employed by antivirus software.

Generally speaking, detection methods can be classified into one of two categories:

-   Static Detection
-   Dynamic / Heuristic / Behavioural Detection

Modern Antivirus software will usually rely on a combination of these.  

---

Static detection methods usually involve some kind of signature detection. A very rudimentary system, for example, would be taking the hashsum of the suspicious file and comparing it against a database of known malware hashsums. This system does tend to be used; however, it would never be used by itself in modern antivirus solutions. For this reason it's usually a good idea to change _something_ when working with a known exploit. The smallest change to the file will result in a completely different hashsum, so even something as small as changing a string in the help message would be enough to bypass this kind of rudimentary detection system.

Fortunately (or unfortunately for us as hackers), this is usually nowhere near enough to bypass static detection methods.

The other form of static detection which is often used in antivirus software (to much greater effect) is a technique called Byte (or string) matching.![](https://assets.tryhackme.com/additional/wreath-network/MDliMWY4MjNl.png) Byte matching is another form of signature detection which works by searching through the program looking to match sequences of bytes against a known database of bad byte sequences. This is much more effective than just hashing the entire file! Of course, it also means that we (as hackers) have a much harder job tracking down the exact line of code responsible for the flag.  

The tradeoff with this method is, of course, speed. Checking small sequences of bytes against a potentially huge program with multiple libraries can take a comparatively long time compared to the milliseconds it would take to hash the entire file and compare the hash against a database. As such, a compromise is sometimes made whereby the AV program hashes small sections of the file to check against the database, rather than hashing the entire thing. This obviously reduces the effectiveness of the technique, but does increase the speed somewhat.  

---

Where static virus malware detection methods look at the file itself, dynamic methods look at how the file _acts._ There are a couple of ways to do this.

1.  AV software can go through the executable line-by-line checking the flow of execution. Based on _pre-defined rules_ about what type of action is malicious (e.g. is the program reaching out to a known bad website, or messing with values in the registry that it shouldn't be?), the AV can see how the program _intends_ to act, and make decisions accordingly
2.  The suspicious software can outright be executed inside a sandbox environment under close supervision from the AV software. If the program acts maliciously then it is quarantined and flagged as malware

![](https://assets.tryhackme.com/additional/wreath-network/YzZkODljOGJm.png)Evading these measures is still perfectly possible, although a lot harder than evading static detection techniques. Sandboxes tend to be relatively distinctive, so we just need to look for various system values (e.g. is there a fan installed, is there a GUI, and if so, what resolution is it, are there any distinctive tools or services running -- `VMtools` for VMware virtual machines, for example) and check to see if there are any red flags. For example, a machine with no fan, no GUI and a classic VM service running is very likely to be a sandbox -- in which case the program should just exit. If the program exits without doing anything malicious then the AV software is fooled into believing that it's safe and allows it to be executed on the target.

Equally, with logic-flow analysis, the AV software is still only working with a set of rules to check malicious behaviour. If the malware acts in a way that is unexpected (e.g. has some random code that does the grand sum of nothing inserted into the exploit) then it will likely pass this detection method.

In addition to this, when working with certain kinds of delivery methods, password protecting the file can get straight around the behavioural analysis checks as (unlike the user who knows the password), the AV software is unable to open and execute the file.

That said, dynamic detection methods are usually a lot more effective than static methods. The drawback is, once again, the time and resources required to spin up a VM to analyse the file in, or go through it line-by-line to see if it's doing anything malicious. These are actions that take time (causing users to grow impatient), and use up a lot of the computer's available resources. Once again the AV has to compromise, using a combination of dynamic and static analysis when scanning a file.  

---

To make life harder still, antivirus vendors are usually in close contact with one another -- as well as with scanning sites such as [VirusTotal](https://www.virustotal.com/). When the AV detects a suspicious file, it usually sends the file back to servers owned by the provider where it gets analysed and shared with other providers. What this means is that once our payload is detected on one computer, the chances are that it will quickly be taken apart and shielded against. This rapid sharing of information allows AV providers to stay ahead of bad actors (a good thing), but also obviously adds an extra complication into our job as Ethical Hackers.

Additionally, new techniques are being developed all the time. For example, many attempts are being made to use machine learning techniques to dynamically update the list of bad behaviours in a sandbox environment, or the rule-lists used in logic-flow analysis of a suspicious file. If you're interested in some of the work being done in this area, TryHackMe's very own [CMNatic](https://cmnatic.co.uk/) did his dissertation on the subject, which can be read [here](https://resources.cmnatic.co.uk/Presentations/Dissertation/).