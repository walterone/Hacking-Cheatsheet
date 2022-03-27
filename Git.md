## Comandi Base

#### git Commit | Crea repo
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


#### git Pull | Aggiorna repo

```bash
#PULL e' una combinazione di 'git fetch' e 'git merge' 

git pull 'remote_name' 'branch_name'
#PULL e' una combinazione di 'git fetch' e 'git merge' 
```