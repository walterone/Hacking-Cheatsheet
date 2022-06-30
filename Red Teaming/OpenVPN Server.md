## NAT Addresses bridging between LAN and VPN
AKA: Make the VPN endpoint in the LAN to reach other host directoly without that proxychains mumbojumbo.

Guide: https://forums.hak5.org/topic/42077-packet-squirrel-wiki-repository/?tab=comments#comment-298972


``` bash
mkdir /etc/openvpn/ccd
```
And inside you create a file with the same name as the client that you want to act as the network brdige:

`iroute 192.168.0.0 255.255.255.0`

in /etc/openvpn/server/server.conf:

```
client-to-client						
client-config-dir /etc/openvpn/ccd/		
push "route 192.168.0.0 255.255.255.0"	
route 192.168.0.0 255.255.255.0
```

![[Pasted image 20220522213447.png]]

Infine sull' host destinatario (Finora provato solo con Packet Squirrel) impostare queste due regole iptables:

``` bash
# Packets flowing from 10.9.20.0/24 (tun0) to 192.168.0.0/24 (br-lan) should be accepted and forwarded
iptables -I FORWARD -i tun0 -o br-lan -s 10.9.20.0/24 -d 192.168.0.0/24 -m conntrack --ctstate NEW -j ACCEPT

# Masquerade packets coming from 10.9.20.0/24 as coming from the PS' WAN IP 
iptables -t nat -I POSTROUTING -o br-lan -s 10.9.20.0/24 -j MASQUERADE
```

Nel mio caso specifico:
```bash
iptables -I FORWARD -i tun0 -o br-lan -s 10.8.0.0/24 -d 192.168.100.0/24 -m conntrack --ctstate NEW -j ACCEPT

iptables -t nat -I POSTROUTING -o br-lan -s 10.8.0.0/24 -j MASQUERADE
```

o per raspberry pi (sto provando se funziona)
```bash
iptables -I FORWARD -i tun0 -o eth0 -s 10.8.0.0/24 -d 192.168.100.0/24 -m conntrack --ctstate NEW -j ACCEPT

iptables -t nat -I POSTROUTING -o eth0 -s 10.8.0.0/24 -j MASQUERADE




iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i tun0 -o eth0 -j ACCEPT

```

## OpenVPN Juice

Per fermare/avviare il servizio

```bash
systemctl stop openvpn-server@server.service
systemctl start openvpn-server@server.service

```

RoadWarrior Server Installer

https://github.com/Nyr/openvpn-install

