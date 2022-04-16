WTF is this hash?:
- https://hashkiller.co.uk
- ```hash-identifier```
- ```hashid```

Esempi hashes hashcat (`hash-identifier`): https://hashcat.net/wiki/doku.php?id=example_hashes
Esempi hashes per John: https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats


# Hashcat (GPU)
```bash
#Esempio con md5crypt + rimozione file originale
hashcat --force -m 500 -a 0 -o found1.txt --remove puthasheshere.hash /usr/share/wordlists/rockyou.txt

#Esempio worpdress hashes
hashcat --force -m 400 -a 0 -o found1.txt wphash.hash /usr/share/wordlists/rockyou.txt

#Esempio AS-REP hash senza file
hashcat -m18200 '$krb5asrep$23$spot@offense.local:3171EA207B3A6FDAEE52BA247C20362E$56FE7DC0CABA8CB7D3A02A140C612A917DF3343C01BCDAB0B669EFA15B29B2AEBBFED2B4F3368A897B833A6B95D5C2F1C2477121C8F5E005AA2A588C5AE72AADFCBF1AEDD8B7AC2F2E94E94CB101E27A2E9906E8646919815D90B4186367B6D5072AB9EDD0D7B85519FBE33997B3D3B378340E3F64CAA92595523B0AD8DC8E0ABE69DDA178D8BA487D3632A52BE7FF4E786F4C271172797DCBBDED86020405B014278D5556D8382A655A6DB1787DBE949B412756C43841C601CE5F21A36A0536CFED53C913C3620062FDF5B18259EA35DE2B90C403FBADD185C0F54B8D0249972903CA8FF5951A866FC70379B9DA' -a 3 /usr/share/wordlists/rockyou.txt
```


#### Full Power
I have found that I can squeeze some more power out of my hash cracking by adding these parameters:
```
--force -O -w 4 --opencl-device-types 1,2
```
- These will force Hashcat to use the CUDA GPU interface which is buggy but provides more performance (–force)
- Will Optimize for 32 characters or less passwords (-O) and 
- will set the workload to "Insane" (-w 4) which is supposed to make your computer effectively unusable during the cracking process.
- Finally "--opencl-device-types 1,2 " will force HashCat to use BOTH the GPU and the CPU to handle the cracking.

**Da verificare le differenze in caso di SLI GPU**





#### HashCat One Rule to Rule them All  
Not So Secure has built a custom rule that I have had luck with in the past:  
https://www.notsosecure.com/one-rule-to-rule-them-all/  
The rule can be downloaded from their Github site:  
https://github.com/NotSoSecure/password_cracking_rules  

I typically drop OneRuleToRuleThemAll.rule into the rules subfolder and run it like this from my windows box (based on the notsosecure article):
```
hashcat64.exe --force -m300 --status -w3 -o found.txt --remove --potfile-disable -r rules\OneRuleToRuleThemAll.rule hash.txt rockyou.txt
```


# John The Ripper (CPU)
Hashcat spesso (ma non sempre) riesce bene ad individuare il tipo di hash che deve craccare. Altrimenti con `--format=` e' possibile specificarne il formato.

```bash
#Esempio NTLM
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

#Esempio NTLM specificando il formato
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt --format=NT

#Una volta craccato basta fare
john --show
```

Ci sono vari strumenti per convertire archivi protetti in file hash digeribili per john
#TODO 


# More
#### Online Rainbow Tables
https://crackstation.net/
http://www.cmd5.org/
https://hashkiller.co.uk/md5-decrypter.aspx
https://www.onlinehashcrack.com/
http://rainbowtables.it64.com/
http://www.md5online.org/