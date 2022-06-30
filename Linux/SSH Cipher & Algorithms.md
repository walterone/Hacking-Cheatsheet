#### Unsupported keys protocols?
add "-oHostKeyAlgorithms=+ssh-dss" in the command

Example related to [[Packet Squirrel]]
```bash
ssh -oHostKeyAlgorithms=+ssh-rsa root@172.16.32.1 
```