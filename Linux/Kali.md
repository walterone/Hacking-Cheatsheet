# Updating Kali
The following updates should be applied (and can be done safely) on the [32-bit PWK Kali VM](https://support.offensive-security.com/pwk-kali-vm/) periodically, especially before a major event (such as an exam attempt or lab extension).

#### apt
```bash
apt get update
apt get dist-upgrade
```

#### searchsploit
```bash
searchsploit -u
```

#### nmap
```bash
nmap --script-updatedb
```

#### wpscan
```bash
wpscan --update
```

#### locate
```bash
updatedb
```


# Tips & Tricks
#### Which process is using the port?
```bash
echo kill $(sudo netstat -anp | awk '/ LISTEN / {if($4 ~ ":80$") { gsub("/.*","",$7); print $7; exit } }')
sudo  kill $(sudo netstat -anp | awk '/ LISTEN / {if($4 ~ ":80$") { gsub("/.*","",$7); print $7; exit } }')
```

#### Find all files newer than 10 August 2021
```bash
sudo find / -type f -newermt 2021-08-10 -ls 2> /dev/null 
```

## Sorting
#### sort
Utilizza come indici le colonne del risultato, e le ordina asc/desc


**By date**
```bash 
#queste sono le colonne per l-output di find
find . -type f 2> /dev/null | sort -k 8,10
```

# Bash/zsh
[[Bash Config & Customization]]
