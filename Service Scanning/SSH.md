## Connection
```bash
ssh james@10.10.188.168
ssh james@10.10.188.168 -i id_rsa
ssh james@10.10.188.168 -i id_rsa -p 2222
#Se da error "no matching host key type found"
ssh james@10.10.188.168 -oHostKeyAlgorithms=+ssh-rsa

#Se da errore di cipher, soluzione simile (#todo)

```


## SCP
sintax is:
*scp \[args\] \<Source\> \<Destination\>*

```bash
#To upload to srv
scp -P 7500 ./met.elf firefart@10.90.60.80:'/tmp' 

#To download from srv
scp -P 7500 firefart@10.90.60.80:'/tmp/file.elf' met.elf 
```

## Heartbleed
#TODO

## Blue Team Considerations:
[[Linux Hardening & Logging]]
