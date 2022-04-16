## Intro
![[Pasted image 20220414172711.png]]

Wiki: https://docs.hak5.org/packet-squirrel/getting-started

#### Defaults
-   Username: `root`
-   Password: `hak5squirrel`
-   IP Address: `172.16.32.1`


## Accesso Remoto (Reverse VPN)
Praticamente il PS agisce da client OVPN e si collega ad un server di nostra scelta (AWS, VPS, etc..) disponibile ONLINE.

UNa volta collegato al server noi ci colleghiamo in SSH alla VPS (VPN Server) e accediamo in ssh dall'indirizzo individuato del PS sull-interfaccia TUN0.

Da qui poi sembra che sul PS abbiamo una suite di tool per fare attivita' di pentesting ma e' ancora da capire se possiamo usare le nostre kali via proxychains

[[ProxyChaining]]

#### Test 1
In: Macbook
Out: Switch di rete

vps: walula.it

**In Arming Mode**
Cambia subnet, tiene raggiungibilita' rete ma non pingo piu' la mia lan:
![[Pasted image 20220414181933.png]]


Su VPN:
Installo ovpn:
```bash
wget https://git.io/vpn -O openvpn.sh && bash openvpn.sh

```

creo certificato e lo metto sul PS.

Ora riavvio PS e dovrei pingarlo dalla VPN.

**In Payload mode**
diventa praticamente invisibile, non ho cambiato i collegamenti
![[Pasted image 20220414182508.png]]