## Comandi Base

#### GIT Commit | Crea repo
```bash
#Nella cartella con i file del progetto
git init

git add .
#Controlla stato dei file modificati e dei push da committare
git status

git commit -m 'your message'
#Prendere URL da quick setup github
git remote add origin 'your_url_name'

git push -u origin master
#User: walterone
#Pass: <token>
```
**Attenzione, git non usa pie' autenticazione password. E' necessario generarsi un [Token](https://github.com/settings/tokens)**


#### GIT Pull | Sync repo locale
PULL e' una combinazione di 'git fetch' e 'git merge'.
Usato per sincronizzare i file locali con quelli remoti.

E' **mandatorio** farlo prima di un push se sono stati creati file direttamente da github.com
```bash
git pull 'remote_name' 'branch_name'

#O anche semplicemente
git pull
```

#### GIT Push | Pushare modifiche locali
```bash
git status

git add .

git commit -m 'altro messaggio'

git push