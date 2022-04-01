Port 445 TCP

## enum4linux-ng
```bash
#Full, force guest session with wrong credentials
enum4linux-ng 89.108.102.21 -A -R -u 'g' -p 'h'

#Full, use on DC
enum4linux-ng 89.108.102.21 -A -R -d -u 'g' -p 'h'

#Stealthier, No RID bruteforcing
enum4linux-ng 89.108.102.21 -As

```

## smbmap
```bash
#Force guest session with wrong credentials
smbmap -H 89.108.102.21 -u 'h' -p 'h'

#Anonymous Session
smbmap -H 89.108.102.21 

#Stealthier, No RID bruteforcing
enum4linux-ng 89.108.102.21 -As

```

## Download Files

```bash
#Scarica TUTTI i files nella cartella
smbget -R smb://192.168.212.71/backups  
```

* PsExec
* 