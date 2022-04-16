## File di config
Attenzione alla differenza tra i vari file **bashrc**, **bash_history**, **bash_aliases** per le diverse shell!

In kali usiamo di default ZSH e non BASH, di conseguenza modificare i relativi files.

Dopo aver modificato il file alias, bisogna fare un ``` source ~/.zsh_aliases``` nel .zshrc

## Aliases
```bash
alias ic='ifconfig'
unalias ic
```

My .zsh_aliases
```bash
alias ll='ls -llah'
alias ic='ifconfig'
alias opt='cd /opt'
alias cwd=pwd
alias clip='xsel --clipboard'
alias pg_vpn='sudo openvpn OVPN/pg.ovpn'
hexc()
{
perl -le "print hex('$1');"
}
alias udp-proto-scanner='/opt/udp-proto-scanner/udp-proto-scanner.pl'

```



## History
```bash
history #Mostra history

#Env-Vars
import HISTIGNORE
import HISTCONTROL
import HISTTIMEFORMAT
```


