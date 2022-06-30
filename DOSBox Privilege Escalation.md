## Intro

DOSBox is a DOS emulator for linux and mac.
It's generally used in retro gaming to run vintage videogames and other softwares. If set with the SUID bit  it's possible to escalate privileges.

**But this works only if you have desktop access** since a new Dosbox process wil spawn in a new windows (kinda like the runas command)

Gli step sono:

 1. Assicurarsi che dosbox abbia SUID attivo
 2. Accedo al desktop via VNC o GUI
 3. Avvio dosbox
 4. faccio `MOUNT C /` per montare come C: la root
 5. ora posso interagire col system as root `type C:\etc\shadow`

Per scalare i privilegi ora bisogna bypassare il problema che dosbox non permette di copiare dalla sua finestrella, allora si puo':

6. Copiare /etc/passwd in /tmp
7. scaricarlo e aggiungere root user in locale
8. caricarlo via ssh o http su server
9. da dosbox fare `copy` del file alla sua posizione originale
10. ora fare  `su <utenza>`