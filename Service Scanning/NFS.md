**Default port**: 2049

## Enumeration

### Useful nmap scripts

```bash

nfs-ls #List NFS exports and check permissions

nfs-showmount #Like showmount -e

nfs-statfs #Disk statistics and info from NFS share

```

### Useful metasploit modules

```bash

scanner/nfs/nfsmount #Scan NFS mounts and list permissions

```

### Mounting

To know **which folder** has the server **available** to mount you an ask it using:

```bash
#These commands can be run locally or remotely

showmount -e <IP>
rpcinfo -p 

```

Then mount it using:

```bash

mount -t nfs [-o vers=2] <ip>:<remote_folder> <local_folder> -o nolock

```

You should specify to **use version 2** because it doesn't have **any** **authentication** or **authorization**.

**Example:**

```bash

mkdir /mnt/new_back

mount -t nfs [-o vers=2] 10.12.0.150:/backup /mnt/new_back -o nolock

#OR

sudo mount -t nfs  10.10.60.134:/opt/conf ./nfsmount -o nolock
   

```

### Mounting special cases!!!
Overpass 3 THM, see training logs.
In questa macchina ho trovato un NFS accessibile **solo** localmente. Faccio forwarding ssh e mi ritrovo nella situazione in cui ho questa porta aperta ma non riesco a montare la share:

![[Pasted image 20220629190524.png]]

A questo punto scopro che e' anche possibile montare le share NFS (non mi chiedo il perche' sinceramente, forse e' l'asterisco o l'opzione "insecure"?) senza specificare il path...

```bash
#No path specified & Different Port & Opzione rw

sudo mount -o rw,port=4500 -t nfs 127.0.0.1: /home/kali/pentests/THM/Overpass/3/pe/ 
```

## Permissions

If you mount a folder which contains **files or folders only accesible by some user** (by **UID**). You can **create** **locally** a user with that **UID** and using that **user** you will be able to **access** the file/folder.

## NSFShell

To easily list, mount and change UID and GID to have access to files you can use [nfsshell](https://github.com/NetDirect/nfsshell).

[Nice NFSShell tutorial.](https://www.pentestpartners.com/security-blog/using-nfsshell-to-compromise-older-environments/)

## Config files

```

/etc/exports

/etc/lib/nfs/etab

```

## Privilege Escalation using NFS misconfigurations

[NFS no\_root\_squash and no\_all\_squash privilege escalation](../linux-hardening/privilege-escalation/nfs-no\_root\_squash-misconfiguration-pe.md)