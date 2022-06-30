Centos moderno ha di default selinux. Se voglio fare modifiche come cambiare la porta di default ssh questo non e' possibile senza toccare SELINUX



More infos --> [[SELinux]]

A lvl di porte, selinux assegna ogni porta ad un servizio di default, anche se non e' installato sul sistema.

Per vedere come sono istanziate fare (as root):

```bash
semanage port -l 
```

Per aggiungerne una ad un profilo:
```bash
#add
semanage port -a -t ssh_port_t -p tcp 25522

#modify (pero' la porta deve essere gia assegnata. Si sta modificando la porta non il profilo)
semanage port --modify -t ssh_port_t -p tcp 25522
```

Infine per eliminare
```bash
semanage port -d -p tcp 25565
```
