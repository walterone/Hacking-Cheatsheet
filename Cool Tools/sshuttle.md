Tool pazzesco, praticamente crea un tunnel SSH con la vittima e da quel tunnel spawna una nuova interfaccia. Permette di interagire con gli host nella sottorete senza usare proxychains.

```
sudo apt install sshuttle


#sshuttle -r username@address subnet 
sshuttle -r user@172.16.0.5 172.16.0.0/24

#Automode
sshuttle -r username@address -N

#sshuttle -r user@address --ssh-cmd "ssh -i KEYFILE" SUBNET
sshuttle -r user@172.16.0.5 --ssh-cmd "ssh -i private_key" 172.16.0.0/24

```

## Attenzione routing!

Se la macchina di destinazione fa parte della subnet instradata con sshuttle, per evitare di perdere il tunnel bisogna specificare un parametro particolare di filtro:

```
#SRV 
sshuttle -r user@172.16.0.5 172.16.0.0/24 -x 172.16.0.5
```