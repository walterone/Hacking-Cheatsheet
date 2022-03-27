## Connect to ftp

```bash
#Connection
ftp <IP>

#Anonymous:Anonymous (prova anche minuscolo)
ftp ftp://Anonymous:Anonymous@192.168.110.112 

#SSL (Install ftp-ssl)
ftp-ssl <IP>

```

## Passive Mode


## Download all
```bash
wget -m ftp://anonymous:anonymous@10.10.10.98
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.98
```