## Impacket
Attenzione a quale script si usa, la versione senza il prefisso "impacket-" non funzionano!!!

Usare sempre:
```
impacket-GetUserSPNs -request -dc-ip 10.129.71.129 active.htb/svc_tgs
impacket-GetNPUsers
etc...
```