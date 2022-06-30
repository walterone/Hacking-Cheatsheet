Possiamo suare AD Module, Rubeus o PowerView per interrogare ed enumerare gli oggetti AD. Ma se dobbiamo effettuare qeury LDAP da Linux suando python allora ecco alcuni strumenti:

## ldapsearch
```bash
#Null Auth Dump
ldapsearch -x -H ldap://dc01.timelapse.htb -N  -b "dc=timelapse,dc=htb"              

#User login Dump
ldapsearch -x -H ldap://dc01.timelapse.htb -D "legacyy_dev@timelapse.htb" -w thuglegacy -b "dc=timelapse,dc=htb" 
```

https://devconnected.com/how-to-search-ldap-using-ldapsearch-examples/